<template>
  
  <div id="app">
  <link href="https://fonts.googleapis.com/css?family=Nunito" rel="stylesheet">    
    <geoToPostcode />
    
     
  </div>
</template>

<script>
import geoToPostcode from './components/journeyplanner/Geo-To-Postcode.vue'

export default {
  name: 'app',
  components: {
    geoToPostcode
  },
  data: function () {
    return {
      count: 0
    }
  },
  computed:{

  },
  methods:{
  },
  mounted(){
    
  }
}
</script>

<style>
*{
  font-family: 'Nunito', sans-serif;
}
</style>
