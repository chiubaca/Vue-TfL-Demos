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

    <div
      v-bind:class="{inputA:isStartSelected}"
      v-on:click="toggleStart"
    >Start: {{startLocationCoords}}</div>
    <div
      v-bind:class="{inputB:isDestSelected}"
      v-on:click="toggleDest"
    >Destination: {{destLocationCoords}}</div>
    <button v-on:click="search">Search</button>
    <br>
    <div v-if="isLoading">Loading Journeys....</div>
    
    <div class="journeys">
        <div v-for="(journey, index) in journeysArr">  Journey Duration:  {{journey.duration}} {{index}}
            <button v-on:click="drawRoute(index)">See Route</button> 
        </div>
    </div>
    
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
      map: {},
      iconA: L.icon({
        iconUrl: require("../../assets/iconA.png"),
        iconSize: [30, 30]
      }),
      iconB: L.icon({
        iconUrl: require("../../assets/iconB.png"),
        iconSize: [30, 30]
      }),
      pointA: {},
      pointB: {},
      startLocationCoords: "51.50863561745838,-0.10025024414062501",
      destLocationCoords: "51.49709527744871,-0.13732910156250003",
      isStartSelected: true,
      isDestSelected: false,
      journeysArr: [],
      isLoading: false
    };
  },
  computed: {
    isSelectedClass: function() {
      return {
        inputA: this.isStartSelected,
        inputB: this.isDestSelected
      };
    }
  },
  methods: {
    initMap: function() {
      this.map = L.map("mapid");
      this.map.setView([51.505, -0.09], 13);

      //Set tile layer provider
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      }).addTo(this.map);

      //default starting points for A and B markers
      this.pointA = L.marker([51.50863561745838, -0.10025024414062501], {
        icon: this.iconA
      }).addTo(this.map);
      this.pointB = L.marker([51.49709527744871, -0.13732910156250003], {
        icon: this.iconB
      }).addTo(this.map);

      //init map click events
      this.map.on("click", ev => {
        if (this.isStartSelected) {
          this.startLocationCoords = `${ev.latlng.lat},${ev.latlng.lng}`;
          this.pointA.setLatLng(ev.latlng);
        } else if (this.isDestSelected) {
          this.destLocationCoords = `${ev.latlng.lat},${ev.latlng.lng}`;
          this.pointB.setLatLng(ev.latlng);
        }
      });
    },
    toggleStart: function() {
      //if startIsSelected is false
      if (this.isStartSelected === false) {
        if (this.isDestSelected) {
          this.isDestSelected = false;
        }
        //make toggleStart true
        this.isStartSelected = true;
      }
      //else if toggleStart is true
      else if (this.isStartSelected) {
        //alert user to click on a location
        alert("Click on a start location");
      }
    },
    toggleDest: function() {
      //if startIsSelected is false
      if (this.isDestSelected === false) {
        if (this.isStartSelected) {
          this.isStartSelected = false;
        }
        //make toggleStart true
        this.isDestSelected = true;
      }
      //else if toggleStart is true
      else if (this.isDestSelected) {
        //alert user to click on a location
        alert("Click on a destnation location");
      }
    },
    search: function() {
      this.isLoading = true;
      console.log(
        `looking up : /${this.startLocationCoords}/to/${
          this.destLocationCoords
        }`
      );
      fetch(
        `https://api.tfl.gov.uk/journey/journeyresults/${
          this.startLocationCoords
        }/to/${this.destLocationCoords}`
      )
        .then(response => {
          return response.json();
        })
        .then(journeyJSON => {
          this.isLoading = false;
          
          //assign journeys to vue object     
          this.journeysArr = journeyJSON.journeys    

        //    console.log("vue journey array", this.journeysArr)
        //    console.log(journeyArr[0].fare) 
          
          //Test to display all routes into 1 line marker
        //   for(let i in journeyArr){
        //       console.log("fetch result:", journeyArr[i])
        //       // generate a div for each object?
        //       // clicking on the div generates the route on the fly?
        //     for (let y in journeyArr[i].legs){
        //         // console.log(journeyArr[i].legs[y])
        //         let routeLine = JSON.parse(journeyArr[i].legs[y].path.lineString)
        //         L.polyline(routeLine, { color: "red" }).addTo(this.map);
        //     } 
        //   }
        
        })
        .catch(error => console.error("Error:", error));
    },
    drawRoute: function(index){
        console.log("drawing route")
        console.log(index)
        console.log(this.journeysArr[index].legs)
          for (let y in this.journeysArr[index].legs){
                // console.log(journeyArr[i].legs[y])
                let routeLine = JSON.parse(this.journeysArr[index].legs[y].path.lineString)
                L.polyline(routeLine, { color: "red" }).addTo(this.map);
            } 
    }
  },
  mounted() {
    this.initMap();
  }
};
</script>

<style scoped>
#mapid {
  height: 480px;
}

.inputA {
  background-color: rgb(125, 125, 241);
}

.inputB {
  background-color: red;
}
</style>
