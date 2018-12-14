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
    
    <div id="mapid"></div>
    <div v-on:click="selectStart">Start: {{startPointer}} </div>
    <div v-on:click="selectDest">Destination: {{destPointer}}</div>
    <button>Search</button>
  </div>
</template>

<script>
import L from "leaflet";

export default {
  data: function() {
    return {
      count: 0,
      startPointer: "",
      destPointer: "",
      isStartSelected:false,
      isDestSelected:false,
    };
  },
  computed: {},
  methods: {
    initMap: function() {
      //Set starting point for the map  
      var map = L.map("mapid").setView([51.505, -0.09], 13);
      //Set tile layer provider  
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      }).addTo(map);
    },
    selectStart:function(){
        
        console.log("start")
    },
     selectDest:function()  {
               console.log("destination!")
    }
  },
  mounted() {
      this.initMap()
  }
};
</script>

<style scoped>
@import url('../../../node_modules/leaflet/dist/leaflet.css');

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#mapid { height: 180px; }
</style>
