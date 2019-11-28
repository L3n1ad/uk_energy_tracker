<template lang="html">
  <div>
    <p>{{generationmix}}</p>
    <p>{{chartData}}</p>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return {
      originalData: [],
      generationmix: [],
      chartData: []
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
    }

  }
}
</script>

<style lang="css" scoped>
</style>
