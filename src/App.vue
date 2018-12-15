<template>
  
  <div id="app">
     
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

</style>
