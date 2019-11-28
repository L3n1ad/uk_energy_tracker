<template lang="html">
  <div class="chart">
    <form v-on:submit.prevent="handelDataRequest">
      <input type="text" name="" v-model="startDate">
      <input type="text" name="" v-model="endDate">
      <input type="submit" name="" value="See Data">
    </form>

    <GChart
      class="chart"
      type="ColumnChart"
      :data="chartData"
      :options="chartOptions"
    />
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return {
      originalData: [],
      generationmix: [],
      startDate: "",
      endDate: "",
      searchedData: [],
      searchedSortedData: [],
      chartData: [],
      chartOptions: {
        chart: {
          title: "United Kingdom Energy Chart",
          subtitle: "Fuel, Percentage"
        }
      }
    }
  },
  mounted(){
    fetch('https://api.carbonintensity.org.uk/generation')
    .then(response => response.json())
    .then(data => this.originalData = data)

    .then(() => this.getGenerationmix())
    .then(() => this.sortChartData())
  },
  methods: {
    getGenerationmix(){
      this.generationmix = this.originalData.data.generationmix
    },
    sortChartData(){
      const result = [];
      for(let object of this.generationmix){
        result.push(Object.values(object))
      }
      result.unshift(['Fuel', 'Percentage'])
      this.chartData = result
    },
    handelDataRequest(){
      fetch(`https://api.carbonintensity.org.uk/generation/${this.startDate}/${this.endDate}`)
      .then(request => request.json())
      .then(data => this.searchedData = data)
    }
  }
}
</script>

<style lang="css" scoped>
  .chart {
    max-height: 500px;
  }
</style>
