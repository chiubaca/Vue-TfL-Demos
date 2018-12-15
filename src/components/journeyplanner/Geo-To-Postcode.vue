<template>
  <!-- place a two markers on a map
    store  x and y coordinate on the two markers
    run the two coordinates through https://api.tfl.gov.uk/journey/journeyresults/51.501,-0.123/to/n225nb?app_id={{app_id}}&app_key={{app_key}}
    iterate through the legs array
    display all the path.lineString objects on the map canvas
    display all the departure point cooridanates on the map canvas
    clicking/onhover on the lines displays additional information about the steps
  clicking/onhover on the points displaus information about the streetName-->
  <div>
    <leaflet-map></leaflet-map>
    
    <div v-bind:class="{inputA:isStartSelected}" v-on:click="toggleStart">Start: {{startLocation}}</div>
    <div v-bind:class="{inputB:isDestSelected}" v-on:click="toggleDest">Destination: {{destLocation}}</div>
    <button v-on:click="test">Test</button>
  </div>
</template>

<script>
import leafletMap from "./Leaflet-Map.vue";

export default {
  components:{
    "leaflet-map": leafletMap   
  },
  data: function() {
    return {
      count: 0,
      startLocation: "test",
      destLocation: "",
      isStartSelected: true,
      isDestSelected: false
    };
  },
  computed: {
  },
  methods: {

    toggleStart: function() {
        //if startIsSelected is false 
        if(this.isStartSelected === false){
            if(this.isDestSelected){
            this.isDestSelected = false;
            }
        //make toggleStart true
        this.isStartSelected = true;
        }
        //else if toggleStart is true
        else if(this.isStartSelected){
          //alert user to click on a location
          alert("Click on a start location")
        }
        
    },
    toggleDest: function() {
        //if startIsSelected is false 
        if(this.isDestSelected === false){
            if(this.isStartSelected){
            this.isStartSelected = false;
            }
        //make toggleStart true
        this.isDestSelected = true;
        }
        //else if toggleStart is true
        else if(this.isDestSelected){
          //alert user to click on a location
          alert("Click on a destnation location")
        }
    },
    test: function(){
        console.log("Testing 123")
    } 
  },
  mounted() {

  }
};
</script>

<style scoped>

#mapid {
  height: 480px;
}

.inputA{
    background-color: blue
}

.inputB{
    background-color: red
}
</style>
