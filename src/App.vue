<template lang="html">
  <div class="chart">
    <form v-on:submit.prevent="handelDataRequest">
      <input type="text" name="" v-model="startDate">
      <input type="text" name="" v-model="endDate">
      <input type="submit" name="" value="See Data">
    </form>

    <GChart
      class="chart"
      type="LineChart"
      :data="chartData"
      :options="chartOptions"
    />
    <GChart
      class="chart"
      type="LineChart"
      :data="testData"
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
      testData: [
        ['Year', 'Sales', 'Expenses', 'Profit'],
        ['2014', 1000, 400, 200],
        ['2015', 1170, 460, 250],
        ['2016', 660, 1120, 300],
        ['2017', 1030, 540, 350]
      ],
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
      .then(data => console.log(data))
    }
  }
}
</script>

<style lang="css" scoped>
  .chart {
    max-height: 500px;
  }
</style>
