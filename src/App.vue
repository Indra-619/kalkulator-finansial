<template>
  <div id="app">
    <div class="title">
      <h1>Kelompok Angin</h1>
      <p>Bayu Yunan (91124041)</p>
      <p>Fahrur Rahman Chermawan (91124045)</p>
      <p>Indra Wahyudi Effendi (91124046)</p> 
      <p>Rizqi Amalia Rais (91124016)</p>
      <p>Yuda Pamungkas (91124048)</p>
    </div>
    
    <h1>Perhitungan Investasi</h1>
    <FinancialCalculator />
  </div>
</template>

<script>
import FinancialCalculator from './components/FinancialCalculator.vue';

export default {
  name: 'App',
  components: {
    FinancialCalculator
  }
};
</script>

<style>
/* Style CSS Global */
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
}
</style>
