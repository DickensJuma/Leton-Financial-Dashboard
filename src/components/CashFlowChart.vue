<template>
    <a-card title="Cash Flow Overview">
      <template #extra>
        <a-space>
          <a-range-picker v-model:value="dateRange" format="YYYY-MM" picker="month" />
          <a-button type="default" size="small" @click="exportToCSV">Export CSV</a-button>
          <a-button type="default" size="small" @click="exportToPDF">Export PDF</a-button>
        </a-space>
      </template>
  
      <canvas ref="chartRef" id="cashChart" style="max-height: 400px;"></canvas>
    </a-card>
  </template>
  
  
  <script setup>
import { ref, computed, onMounted, watch } from 'vue'
import dayjs from 'dayjs'
import Chart from 'chart.js/auto'
import Papa from 'papaparse'
import jsPDF from 'jspdf'
import html2canvas from 'html2canvas'
import { cashFlow as rawCashFlow } from '../data/financialData.js'

// State
const chartRef = ref(null)
const dateRange = ref([dayjs('2025-01'), dayjs('2025-12')]) // full year default

// Filtered cash flow by range
const filteredCashFlow = computed(() =>
  rawCashFlow.filter(entry => {
    const entryDate = dayjs(entry.date)
    return entryDate.isAfter(dateRange.value[0].startOf('month').subtract(1, 'day')) &&
           entryDate.isBefore(dateRange.value[1].endOf('month').add(1, 'day'))
  })
)

const netCash = computed(() =>
  filteredCashFlow.value.map(row => row.inflow - row.outflow)
)

const netCashColors = computed(() =>
  netCash.value.map(v => (v < 0 ? '#f5222d' : '#52c41a'))
)

onMounted(() => {
  renderChart()
})

const renderChart = () => {
  const ctx = chartRef.value
  if (!ctx) return
  if (Chart.getChart(ctx)) Chart.getChart(ctx).destroy()

  const data = filteredCashFlow.value

  new Chart(ctx, {
    type: 'bar',
    data: {
      labels: data.map(row => row.date),
      datasets: [
        {
          label: 'Inflow (€)',
          data: data.map(row => row.inflow),
          backgroundColor: 'rgba(75, 192, 192, 0.7)'
        },
        {
          label: 'Outflow (€)',
          data: data.map(row => row.outflow),
          backgroundColor: 'rgba(255, 99, 132, 0.7)'
        },
        {
          label: 'Net Cash (€)',
          data: netCash.value,
          backgroundColor: netCashColors.value,
          type: 'line',
          borderWidth: 2,
          tension: 0.4,
          fill: false
        }
      ]
    },
    options: {
      responsive: true,
      animation: { duration: 1500, easing: 'easeOutBounce' },
      plugins: {
        legend: { position: 'top' },
        title: { display: true, text: 'Cash Flow (Filtered)' },
        tooltip: {
          callbacks: {
            label: ctx => `${ctx.dataset.label}: €${ctx.parsed.y}`
          }
        }
      },
      scales: {
        y: { beginAtZero: true, title: { display: true, text: '€ Amount' } },
        x: { title: { display: true, text: 'Month' } }
      }
    }
  })
}

// Watch range changes and re-render
watch(dateRange, () => renderChart())

const exportToCSV = () => {
  const rows = filteredCashFlow.value.map((row, i) => ({
    Date: row.date,
    Inflow: row.inflow,
    Outflow: row.outflow,
    'Net Cash': netCash.value[i]
  }))

  const timestamp = dayjs().format('YYYY-MM-DD_HH-mm')
  const csv = Papa.unparse(rows)
  const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' })
  const link = document.createElement('a')
  link.href = URL.createObjectURL(blob)
  link.download = `Leton_Cashflow_${timestamp}.csv`
  link.click()
}

const exportToPDF = async () => {
  const canvas = await html2canvas(chartRef.value.closest('div'))
  const imgData = canvas.toDataURL('image/png')
  const pdf = new jsPDF('landscape')
  const imgProps = pdf.getImageProperties(imgData)
  const pdfWidth = pdf.internal.pageSize.getWidth()
  const pdfHeight = (imgProps.height * pdfWidth) / imgProps.width

  pdf.setFontSize(18)
  pdf.text('Leton Cash Flow Report', 10, 10)
  pdf.setFontSize(11)
  pdf.text(`Exported: ${dayjs().format('YYYY-MM-DD HH:mm')}`, 10, 18)
  pdf.addImage(imgData, 'PNG', 0, 25, pdfWidth, pdfHeight)
  pdf.save(`Leton_Cashflow_${dayjs().format('YYYY-MM-DD_HH-mm')}.pdf`)
}
</script>

  