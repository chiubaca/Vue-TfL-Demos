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

    <div v-bind:class="{inputA:isStartSelected}" v-on:click="toggleStart">Start: {{startLocation}}</div>
    <div
      v-bind:class="{inputB:isDestSelected}"
      v-on:click="toggleDest"
    >Destination: {{destLocation}}</div>
    <!-- <button v-on:click="test">Test</button> -->
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
      startLocation: "test",
      destLocation: "",
      isStartSelected: true,
      isDestSelected: false
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

      this.pointA = L.marker([51.505, -0.09], { icon: this.iconA }).addTo(this.map);
      this.pointB = L.marker([51.505, -0.08], { icon: this.iconB }).addTo(this.map);

      //init map click events
      this.map.on("click", ev => {
        if (this.isStartSelected) {
          this.startLocation = ev.latlng;
          this.pointA.setLatLng(ev.latlng)
     
        } 
        else if (this.isDestSelected) {
          this.destLocation = ev.latlng;
          this.pointB.setLatLng(ev.latlng)
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
