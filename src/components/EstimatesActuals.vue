<template>
    <a-card title="Summary">
      <p><strong>Total Estimated:</strong> €{{ totalEst }}</p>
      <p><strong>Total Actual:</strong> €{{ totalAct }}</p>
    </a-card>
  
    <a-table
      :columns="columns"
      :data-source="filteredData"
      :pagination="{ pageSize: 5 }"
      row-key="id"
    >
      <template #customFilterDropdown="{ setSelectedKeys, selectedKeys, confirm, clearFilters, column }">
        <div style="padding: 8px">
          <a-input
            v-model:value="selectedKeys[0]"
            @pressEnter="confirm()"
            @input="confirm()"
            placeholder="Search"
            style="width: 188px; margin-bottom: 8px; display: block"
          />
          <a-space>
            <a-button type="primary" size="small" @click="confirm()">Search</a-button>
            <a-button size="small" @click="clearFilters()">Reset</a-button>
          </a-space>
        </div>
      </template>
    </a-table>
  </template>
  
  <script setup>
  import { ref, computed } from 'vue'
  import { estimatesActuals } from '../data/financialData.js'
  
  const rawData = estimatesActuals.map(row => ({
    ...row,
    total: row.actRev - row.actCost,
    profit: (((row.actRev - row.actCost) / row.actRev) * 100).toFixed(2) + '%'
  }))
  
  const searchText = ref('')
  const filteredData = computed(() => rawData)
  
  const generateColumn = (title, key) => ({
    title,
    dataIndex: key,
    key,
    customFilterDropdown: true,
    onFilter: (value, record) => String(record[key]).toLowerCase().includes(value.toLowerCase()),
    filterSearch: true,
    sorter: (a, b) => (typeof a[key] === 'number' ? a[key] - b[key] : a[key].localeCompare(b[key]))
  })
  
  const columns = [
    generateColumn('Item', 'item'),
    generateColumn('Est Cost', 'estCost'),
    generateColumn('Act Cost', 'actCost'),
    generateColumn('Est Rev', 'estRev'),
    generateColumn('Act Rev', 'actRev'),
    generateColumn('Total (€)', 'total'),
    generateColumn('Profit %', 'profit')
  ]
  
  const totalEst = computed(() =>
    estimatesActuals.reduce((sum, item) => sum + item.estCost + item.estRev, 0)
  )
  const totalAct = computed(() =>
    estimatesActuals.reduce((sum, item) => sum + item.actCost + item.actRev, 0)
  )
  </script>
  