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
    <div v-bind:class="{inputA:isStartSelected}" v-on:click="toggleStart">Start: {{startPointer}}</div>
    <div v-bind:class="{inputB:isDestSelected}" v-on:click="toggleDest">Destination: {{destPointer}}</div>
    <button>Search</button>
  </div>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";

/* fix for marker icons not showing - 
https://github.com/KoRiGaN/Vue2Leaflet/issues/157 */
delete L.Icon.Default.prototype._getIconUrl;
L.Icon.Default.mergeOptions({
  iconRetinaUrl: require("leaflet/dist/images/marker-icon-2x.png"),
  iconUrl: require("leaflet/dist/images/marker-icon.png"),
  shadowUrl: require("leaflet/dist/images/marker-shadow.png")
});

export default {
  data: function() {
    return {
      count: 0,
      startPointer: "",
      destPointer: "",
      isStartSelected: false,
      isDestSelected: true
    };
  },
  computed: {
      isSelectedClass:function(){
          return{
              inputA:this.isStartSelected,
              inputB:this.isDestSelected
          }
      }
  },
  methods: {
    initMap: function() {
      var myIcon = L.icon({
        iconUrl: require("../../assets/iconA.png"),
        iconSize: [30, 30],
        // iconAnchor: [22, 94],
        // popupAnchor: [-0, -0],
        // shadowUrl: "my-icon-shadow.png",
        // shadowSize: [68, 95],
        // shadowAnchor: [22, 94]
      });
      //Set starting point for the map
      let map = L.map("mapid").setView([51.505, -0.09], 13);
      //Set tile layer provider
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      }).addTo(map);

      L.circle([51.508, -0.11], {
        color: "red",
        fillColor: "#f03",
        fillOpacity: 0.5,
        radius: 500
      }).addTo(map);

      L.marker([51.5, -0.09], {icon: myIcon}).addTo(map);

      map.on("click", function(ev) {
        // L.marker(ev.latlng, {icon: myIcon}).addTo(map); // ev is an event object (MouseEvent in this case)
        
        //check if isStartSelected and length is greater than 0
            //make the click location = 
      
      });
    },
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
    }
  },
  mounted() {
    this.initMap();
  }
};
</script>

<style scoped>
/* @import url('../../../node_modules/leaflet/dist/leaflet.css'); */

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

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
