<template>
  
  <div id="app">
  <link href="https://fonts.googleapis.com/css?family=Nunito" rel="stylesheet">    
    <basicJourneyPlanner />
    
     
  </div>
</template>

<script>
import basicJourneyPlanner from './components/BasicJourneyPlanner.vue'

export default {
  name: 'app',
  components: {
    basicJourneyPlanner
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
