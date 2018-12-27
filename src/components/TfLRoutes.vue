<template>
  <!-- 
  get tube meta data via /Line/Mode/tube/Route
  -->
  <div>
    <div class="tube-buttons-container" v-for="(item, index) in tubeLineNames" :key="item.id">
      <div class="tube-buttons" v-bind:id="item" v-on:click="drawRoute(index)" >{{ item }}</div>
    </div>
    <div id="tfl-routes-map"></div>
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
  data() {
    return {
      map: {},
      tubeLineNames: [],
      tubeFeatureGroup: L.featureGroup(),
      testGeoJSON: L.geoJSON()
    };
  },
  methods: {
    getTubeLineNames() {
      fetch("https://api.tfl.gov.uk/Line/Mode/tube/Route")
        .then(response => response.json())
        .then(myJson => {
          this.tubeLineNames = myJson.map(item => item.id);
          console.log(this.tubeLineNames);
        });
    },
    initMap() {
      this.map = L.map("tfl-routes-map");
      this.map.setView([51.505, -0.09], 13);

      //Set tile layer provider
      L.tileLayer(
        "https://maps.tilehosting.com/styles/positron/{z}/{x}/{y}.png?key=u97ZyzhSlgbbrA7IPN6E",
        {
          attribution:
            '<a href="https://www.maptiler.com/license/maps/" target="_blank">© MapTiler</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">© OpenStreetMap contributors</a>'
        }
      ).addTo(this.map);
    },
    drawRoute(tubeName){
      console.log(` Drawing ${this.tubeLineNames[tubeName]}`)
      fetch(`https://api.tfl.gov.uk/Line/${this.tubeLineNames[tubeName]}/route/sequence/outbound`)
      .then(response => response.json())
      .then(resonseJson => {
          
          let lineArray = JSON.parse(resonseJson.lineStrings[0])
          
          let flipedLineString = lineArray[0].map(function(verticies){
            verticies.push(verticies.shift()) 
            return verticies
          })
        

          // L.polyline( JSON.parse(resonseJson.lineStrings[0]) ,{ color: "red",weight: 3}).addTo(this.map)
          this.tubeFeatureGroup.addLayer(
                  L.polyline( flipedLineString ,{ color: "red",weight: 3})
          );
        });
       this.tubeFeatureGroup.addTo(this.map);  
    }
  },
  mounted() {
    this.initMap();
    this.getTubeLineNames()
  }
};
</script>

<style scoped>
:root {
  --bakerloo:brown;
  --central:brown;
  --circle:brown;
  --district:brown;
  --hammersmith-city:brown;
  --jubilee:brown;
  --metropolitan:brown;
  --northern:brown;
  --piccadilly:brown;
  --victoria:brown;
  --waterloo-city:brown;
}

.tube-buttons-container{
  display: inline-flex;
  justify-content:center
}

.tube-buttons{
   box-shadow: 0px 0px 10px #7d7d7d;
    padding: 0.5em;
    margin:0.5em; 
    border-bottom-style: solid;
    border-color: white 
}

.tube-buttons:hover{
   box-shadow: 0px 0px 8px #7d7d7d;
    padding: 0.5em;
    margin:0.5em;
    border-bottom-style: solid;
    border-color: black 
}

#tfl-routes-map{
  height:350px;
}

</style>
