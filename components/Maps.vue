<template>
<div>
<client-only>
<!-- <div v-for="x in markers.data">
  <h6>{{x.polygon_name}}</h6>
</div> -->
<div>

  <l-map id="map"  :zoom="zoom" :center="center">
    <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
    <div v-for="x in markers.data">
        <l-marker :lat-lng="x.mc"><l-popup>{{x.polygon_name}}</l-popup></l-marker>
    </div>
    
  </l-map>
</div>
</client-only>
</div>

</template>

<script>

//import {LMap, LTileLayer, LMarker} from 'vue2-leaflet';
//import 'leaflet/dist/leaflet.css';


export default {
  name:'Maps',
  // components: {
  //   LMap,
  //   LTileLayer,
  //   LMarker
  // },
  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 5,
      center: [33, 54],
      markerLatLng: [33, 54],
      markers: {},
    };
  },
  methods: {
    async fetch() {
      this.markers = await fetch(
        'http://127.0.0.1:8000/markers/'
      ).then(res => res.json())
  },
    
  
},

 mounted() {
    this.fetch()
},
}
</script>

<style>
#map{
  height: 500px;
  width: 800px;
}

</style>
