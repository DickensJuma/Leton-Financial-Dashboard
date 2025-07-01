<template>
  <a-layout style="min-height: 100vh">
    <!-- Header -->
    <a-layout-header class="leton-header">
      <div class="leton-header-left">
        <img src="/leton-logo.png" alt="Leton" class="logo" />
        <span class="title">Leton Financial Dashboard</span>
      </div>
      <div class="leton-header-right">
        <a-avatar style="background-color: #87d068" icon="L" />
      </div>
    </a-layout-header>

    <!-- Content -->
    <a-layout-content class="leton-content">
      <Dashboard />
    </a-layout-content>

    <!-- Footer -->
    <a-layout-footer class="leton-footer">
      ©2025 Leton • Built By Dickens 
    </a-layout-footer>
  </a-layout>
</template>

<script setup>
import Dashboard from './views/Dashboard.vue'
</script>

<style scoped>
/* HEADER */
.leton-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #001529;
  color: white;
  padding: 0 24px;
}

.leton-header-left {
  display: flex;
  align-items: center;
}

.logo {
  height: 32px;
  margin-right: 12px;
}

.title {
  font-size: 20px;
  font-weight: 600;
  color: #fff;
}

/* CONTENT */
.leton-content {
  padding: 24px;
  background: #f5f5f5;
}

/* FOOTER */
.leton-footer {
  text-align: center;
  background: #f0f2f5;
  color: #888;
  font-size: 13px;
}
</style>
