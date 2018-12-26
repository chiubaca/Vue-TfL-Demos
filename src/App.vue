<template>
  
  <div id="app">
  <link href="https://fonts.googleapis.com/css?family=Nunito" rel="stylesheet">    
    <hr>
    <h1>Dynamically Draws Tube Lines</h1>
    <tflRoutes/>
    <hr>
    <h1>Simple Interactive Journey Planner </h1>
    <basicJourneyPlanner />
    <hr> 
  </div>
</template>

<script>
import basicJourneyPlanner from './components/BasicJourneyPlanner.vue'
import tflRoutes from './components/TfLRoutes.vue'

export default {
  name: 'app',
  components: {
    basicJourneyPlanner,
    tflRoutes
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
