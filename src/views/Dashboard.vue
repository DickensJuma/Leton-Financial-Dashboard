<template>
  <a-tabs>
    <a-tab-pane key="1" tab="Estimates vs Actuals">
      <EstimatesActuals />
    </a-tab-pane>
    <a-tab-pane key="2" tab="Cash Flow">
      <CashFlowChart />
    </a-tab-pane>
  </a-tabs>
</template>

<script setup>
import EstimatesActuals from '../components/EstimatesActuals.vue'
import CashFlowChart from '../components/CashFlowChart.vue'
</script>
