<template>
  <div id="app">
    <AvailabilityTable />
  </div>
</template>

<script>
import AvailabilityTable from './components/AvailabilityTable.vue'

export default {
  name: 'App',
  components: {
    AvailabilityTable
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 1rem;
}
</style>
