<template>
  <!-- 
  get tube meta data via Line/Mode/tube/Route
  generate buttons from the meta data
  clicking on button draws the tube line dynamically pulling a request via /Line/Mode/tube/Route/sequence/outbound

  Improvements
  - handle bad data e.g northern and central line not returning any geometry
  - loading spinner
  - click /on hover information
  -->
  <div>
    <div class="tube-buttons-container" v-for="(item, index) in tubeLineNames" :key="item.id">
      <div class="tube-buttons" v-bind:id="item" v-on:click="drawRoute(index,item)" >{{ item }}</div>
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
      tubeMetaData:{
        "bakerloo":{
          colour:"#894E24"
        },
        "central":{
          colour:"#DC241F"
        },
        "circle":{
          colour:"#FFCE00"
        },
        "district":{
          colour:"#007229"
        },
        "hammersmith-city":{
          colour:"#D799AF"
        },
        "jubilee":{
          colour:"#6A7278"
        },
        "metropolitan":{
          colour:"#751056"
        },
        "northern":{
          colour:"#000000"
        },
        "piccadilly":{
          colour:"#0019A8"
        },
        "victoria":{
          colour:"#00A0E2"
        },
        "waterloo-city":{
          colour:"#76D0BD"
        }
      },
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
    drawRoute(tubeIndex){
      let tubeNameLookup = this.tubeLineNames[tubeIndex];
      console.log(`Drawing ${tubeNameLookup}`)

      fetch(`https://api.tfl.gov.uk/Line/${tubeNameLookup}/route/sequence/outbound`)
      .then(response => response.json())
      .then(resonseJson => {
          //function to flip lat long around becasue tfl API has
          //them the wrong way round and leaflet cant handle it
          let lineArray = JSON.parse(resonseJson.lineStrings[0])
          let flipedLineString = lineArray[0].map(function(verticies){
            verticies.push(verticies.shift()) 
            return verticies
          })

          this.tubeFeatureGroup.addLayer(
                  L.polyline( flipedLineString ,{ color: this.tubeMetaData[tubeNameLookup].colour ,
                                                  weight: 3,
                                                  opacity:0.5})
          );
        })
        .catch(error => console.error("Error:", error));
       this.tubeFeatureGroup.addTo(this.map);
      //  console.log(this.tubeFeatureGroup.getLayers())  
    }
  },
  mounted() {
    this.initMap();
    this.getTubeLineNames()
  }
};
</script>

<style scoped>

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
  --bakerloo:#894E24;
  --central:#DC241F;
  --circle:#FFCE00;
  --district:#007229;
  --hammersmith-city:#D799AF;
  --jubilee:#6A7278;
  --metropolitan:#751056;
  --northern:#000000;
  --piccadilly:#0019A8;
  --victoria:#00A0E2;
  --waterloo-city:#76D0BD;

   box-shadow: 0px 0px 8px #7d7d7d;
    padding: 0.5em;
    margin:0.5em;
    border-bottom-style: solid;
 
}

.tube-buttons#central:hover{
   border-color:  var(--central)
}

.tube-buttons#bakerloo:hover{
   border-color:  var(--bakerloo)
}
.tube-buttons#circle:hover{
   border-color:  var(--circle)
}
.tube-buttons#district:hover{
   border-color:  var(--district)
}
.tube-buttons#hammersmith-city:hover{
   border-color:  var(--hammersmith-city)
}
.tube-buttons#jubilee:hover{
   border-color:  var(--jubilee)
}
.tube-buttons#metropolitan:hover{
   border-color:  var(--metropolitan)
}

.tube-buttons#northern:hover{
   border-color:  var(--northern)
}
.tube-buttons#piccadilly:hover{
   border-color:  var(--piccadilly)
}
.tube-buttons#victoria:hover{
   border-color:  var(--victoria)
}
.tube-buttons#waterloo-city:hover{
   border-color:  var(--waterloo-city)
}

#tfl-routes-map{
  height:350px;
}

</style>
