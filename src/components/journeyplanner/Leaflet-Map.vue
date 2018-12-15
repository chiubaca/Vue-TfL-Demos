<template>
    <div>
        <div id="mapid" ref="mapRef"></div>
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
       pointA:  [51.5, -0.1],
       pointB: [51.5, -0.12],
       myIcon : L.icon({
        iconUrl: require("../../assets/iconA.png"),
        iconSize: [30, 30],
      }),
      map : {}
    };
  },
  computed: {
  },
  methods: {
    // initMap: function() {}
    test:function(){
        console.log(this.pointA)
        this.pointA = this.pointB
    }  
  },
  mounted() {

      //Set starting point for the map
      //   let map = L.map("mapid")
       this.map = L.map("mapid");
       console.log(this.pointA)
       console.log(this.map)

      this.map.setView([51.505, -0.09], 13);
      //Set tile layer provider
      L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
        attribution:
          '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      }).addTo(this.map);

      L.marker(this.pointA, {icon: this.myIcon}).addTo(this.map);     
      //init map click events
    //   map.on("click", function(ev) {
    //     L.marker(ev.latlng, {icon: myIcon}).addTo(map);
    //   });
   
  }
};
</script>

<style scoped>

#mapid {
  height: 480px;
}

</style>
