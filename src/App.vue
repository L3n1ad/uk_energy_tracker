<template lang="html">
  <div class="chart">
    <h1>Most Up To Date </h1>
    <GChart
      class="chart"
      type="ColumnChart"
      :data="chartData"
      :options="chartOptions"
    />
    <h1>Search by date</h1>
    <form v-on:submit.prevent="handelDataRequest">
      <input placeholder="2019-01-20T12:00Z" type="datetime-local" name="" v-model="startDate">
      <input placeholder="2019-01-21T12:00Z" value="2019-01-21T12:00Z" type="datetime-local" name="" v-model="endDate">
      <input type="submit" name="" value="See Data">
    </form>

    <GChart
      style="height: 100%"
      v-if="searchedSortedData.length > 0"
      class="chart"
      type="AreaChart"
      :data="searchedSortedData"
      :options="chartOptions"
    />
    <!-- <p>{{this.searchedSortedData}}</p> -->
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
          hAxis:{title: 'Year', titleTextStyle:{
            color:'red'
          }},
          subtitle: "Fuel, Percentage",
          isStacked: "relative"
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
      .then(() => this.sortSearchedData())
    },
    sortSearchedData(){
      // debugger
      const result = []
      const labelsArray = ["Date"];
      const firstGenerationmix = this.searchedData.data[0].generationmix;
      for(let object of firstGenerationmix){
        labelsArray.push(object.fuel)
      }
      result.push(labelsArray);
      for(let dataSet of this.searchedData.data){
        const dataArray = [];
        dataArray.push(dataSet.from);
        for(let generationmix of dataSet.generationmix){
          dataArray.push(generationmix.perc);
        }
        result.push(dataArray);
      }
      // firstArray.push(Object.keys(this.searchedData.data[0].generationmix))
      // console.log(this.searchedData.data[0]);
      this.searchedSortedData = result
      console.log("data searched");
    }
  }
}
</script>

<style lang="css" scoped>
@import url('https://fonts.googleapis.com/css?family=Lobster&display=swap');


  .chart {
    margin-top: 3em;

  }
  h1{
    text-align: center;
    font-family: 'Lobster', cursive;
    margin-top: 39px


  }
  form{
    text-align: center;
    display: flex;
    justify-content: center;

  }
  input{
    padding: 4px;
    font-family: 'Lobster', cursive;

  }


</style>
