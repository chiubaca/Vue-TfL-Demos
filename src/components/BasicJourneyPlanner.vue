
<template>
  <!-- 
    place a two markers on a map
    store  x and y coordinate on the two markers
    run the two coordinates through https://api.tfl.gov.uk/journey/journeyresults/51.501,-0.123/to/n225nb?app_id={{app_id}}&app_key={{app_key}}
    iterate through the legs array
    display all the path.lineString objects on the map canvas
    display all the departure point cooridanates on the map canvas
    clicking/onhover on the lines displays additional information about the steps
    clicking/onhover on the points displaus information about the streetName
  -->
  <div>
    <div id="user-input">
      <div class="start-input" v-bind:class="{selected:isStartSelected}" v-on:click="toggleStart">
        Start Location:
        <input
          v-model="startLocationCoords"
          placeholder="startLocationCoords"
          type="text"
        >
      </div>

      <div
        class="destination-input"
        v-bind:class="{selected:isDestSelected}"
        v-on:click="toggleDest"
      >
        Destination:
        <input v-model="destLocationCoords" placeholder="Pick Destination" type="text">
      </div>

      <div id="go-button" v-on:click="search">Go!</div>
    </div>

    <div id="map-container">
      <div id="loading-spinner" v-if="isLoading"></div>
      <div id="mapid"></div>
    </div>

    <div class="journeys">
      <div v-for="(journey, index) in journeysArr">
        Journey Duration: {{journey.duration}} minutes
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
        iconUrl: require("./../assets/iconA.png"),
        iconSize: [30, 30]
      }),
      iconB: L.icon({
        iconUrl: require("./../assets/iconB.png"),
        iconSize: [30, 30]
      }),
      pointA: {},
      pointB: {},
      startLocationCoords: "51.506,-0.132",
      destLocationCoords: "51.508,-0.053",
      isStartSelected: true,
      isDestSelected: false,
      journeysArr: [],
      // routeLineArr:[],
      routeFeatureGroup: L.featureGroup(),
      allRoutesFeatureGroup:L.featureGroup(),
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
      L.tileLayer(
        "https://maps.tilehosting.com/styles/positron/{z}/{x}/{y}.png?key=u97ZyzhSlgbbrA7IPN6E",
        {
          attribution:
            '<a href="https://www.maptiler.com/license/maps/" target="_blank">© MapTiler</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">© OpenStreetMap contributors</a>'
        }
      ).addTo(this.map);

      //default starting points for A and B markers
      this.pointA = L.marker([51.506, -0.132], {
        icon: this.iconA
      }).addTo(this.map);
      this.pointB = L.marker([51.508, -0.053], {
        icon: this.iconB
      }).addTo(this.map);

      //init map click events
      this.map.on("click", ev => {
        if (this.isStartSelected) {
          this.startLocationCoords = `${ev.latlng.lat.toFixed(
            3
          )},${ev.latlng.lng.toFixed(3)}`;
          this.pointA.setLatLng(ev.latlng);
        } else if (this.isDestSelected) {
          this.destLocationCoords = `${ev.latlng.lat.toFixed(
            3
          )},${ev.latlng.lng.toFixed(3)}`;
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
        console.log("Click on a start location");
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
        console.log("Click on a destnation location");
      }
    },
    search: function() {
      this.isLoading = true;

      //delete old route if there is one
      this.routeFeatureGroup.clearLayers();
      this.allRoutesFeatureGroup.clearLayers();
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
          // console.log(journeyJSON)
          this.isLoading = false;
          //assign journeys to vue object
          this.journeysArr = journeyJSON.journeys;

          //display all possible routes on the map

          for(let i in this.journeysArr){
              for (let j in this.journeysArr[i].legs){
                let allRoutes = JSON.parse(this.journeysArr[i].legs[j].path.lineString);
                this.allRoutesFeatureGroup.addLayer(
                  L.polyline( allRoutes,  { color: "grey",
                                           weight: 3, 
                                          opacity:0.5
                                          })
                )}
          }      
                this.allRoutesFeatureGroup.addTo(this.map);
        })
        .catch(error => console.error("Error:", error));
    },
    drawRoute: function(index) {
      //delete old route;
      this.routeFeatureGroup.clearLayers();
      //  iterate through all paths in the current leg and draw vertices.
      for (let y in this.journeysArr[index].legs) {
        // this could be used to plot interchange locations
        // let departPoint = this.journeysArr[index].legs[y].departurePoint

        //All route lines are convert to a json object,converted to a leaflet line object, the added in
        //as a new layer into a Feature Group object, this makes it easier to manage later
        let journeyRoute = JSON.parse(
          this.journeysArr[index].legs[y].path.lineString
        );
        this.routeFeatureGroup.addLayer(
          L.polyline(journeyRoute, { color: this.getRandomColour(), weight: 6 })
            .bindPopup(this.journeysArr[index].legs[y].instruction.detailed)
            .on("mouseover ", function(evt) {
              this.openPopup(evt.latlng);
            })
        );
      }
      this.routeFeatureGroup.addTo(this.map);
    },
    getRandomColour: function() {
      var letters = "0123456789ABCDEF";
      var color = "#";
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    }
  },
  mounted() {
    this.initMap();
  }
};
</script>


<style >
#map-container{
    position: relative;
  box-shadow: 0px 0px 13px #7d7d7d;
  margin:0.5em;
  background: #3838ff
}

#mapid {
height: 400px
}

#map-container #loading-spinner {
  z-index: 401;
  margin: 0;
  position: absolute;
  border: 16px solid #f3f3f3; /* Light grey */
  border-top: 16px solid #3498db; /* Blue */
  border-radius: 50%;
  width: 50px;
  height: 50px;
  top:150px;
  left:50%;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

#user-input {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 25px;
  justify-content: space-evenly;
  flex-wrap: wrap
}

#user-input div{
  box-shadow: 0px 0px 13px #7d7d7d;
  padding:0.5em;
}
.start-input {
  border: 1px solid #3838ff;
}
.start-input:hover {
  background-color: #3838ff;
}

.start-input.selected {
  background-color: #3838ff;
}
.destination-input.selected {
  background-color: red;
}
.destination-input {
  border: 1px solid red;
}
.destination-input:hover {
  background-color: red;
}

#go-button {
  display: block;
  height: 30px;
  width: 30px;
  border-radius: 50%;
  /* border: 1px solid black; */
  display: flex;
  justify-content: center;
  align-items: center;
   background-color: grey;

}
#go-button:hover {
  background-color: green;
}

#go-button:active {
 box-shadow: 0px 0px 0px #7d7d7d;
}

.journeys{
  display: flex;
  justify-content: center;
  align-items: center;
  justify-content:space-evenly;
  flex-wrap: wrap

}

.journeys div{
    box-shadow: 0px 0px 13px #7d7d7d;
    padding: 1em;
    margin:0.5em;  
}
</style>
