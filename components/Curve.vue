<template>
<div id="map">
</div>
</template>
<script>

//import {LMap, LTileLayer, LMarker} from 'vue2-leaflet';
//import 'leaflet/dist/leaflet.css';


export default {
  name:'Curve',
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
    curve() {
      const map=L.map('map').setView([33, 54], 1);
      L.tileLayer(this.url, { attribution: this.attribution }).addTo(map);

      const latlngs=[];
      const locations=[
      {
        lat: 40,
        lng: -73,
        lat2: 33,
        lng2: 54
      },
      // {
      //   lat: 32,
      //   lng: 53,
      //   lat2: 41,
      //   lng2: -74
      // }
      ];
      let i =0;
      for(let x of locations){
        const marker1=L.marker([locations[i].lat, locations[i].lng]);
        marker1.addTo(map);
        const marker2=L.marker([locations[i].lat2, locations[i].lng2]);
        marker2.addTo(map)
        var latlng1 =[locations[i].lat, locations[i].lng];
        var latlng2 =[locations[i].lat2, locations[i].lng2];
        i++

        var offsetX= latlng2[1]-latlng1[1],
        offsetY = latlng2[0]-latlng1[0];
        var r= Math.sqrt(Math.pow(offsetX, 2)+Math.pow(offsetY, 2)),
        theta= Math.atan2(offsetY, offsetX);

        var thetaOffset=(3.14/10);
        var r2= (r/2)/(Math.cos(thetaOffset)),
        theta2= theta+thetaOffset;
        var midpointX=(r2*Math.cos(theta2))+latlng1[1],
        midpointY= (r2*Math.sin(theta2))+latlng1[0];
        var midpointLatLng=[midpointY, midpointX];
        latlngs.push(latlng1, midpointLatLng, latlng2);
        var pathOptions={
          color: 'orange',
          weight: 5
        }
        if (typeof document.getElementById('map').animate === "function"){
          var durationBase=1000;
          var duration= Math.sqrt(Math.log(r))*durationBase;
          pathOptions.animate={
            duration: duration,
            iterations: Infinity,
            easing: 'ease-in-out',
            direction : 'alternate'
          }
        }
        var curvedPath=L.curve(
          [
          'M', latlng1,
          'Q', midpointLatLng,
          latlng2
          ], pathOptions).addTo(map);



      }
      
    },
  
},

 mounted() {
    this.curve()
},
}
</script>

<style>
#map{
  height: 500px;
  width: 800px;
}

</style>
