<template>
 <div style="height: 600px; width: 100%;" id="map">
 </div> 
</template>

<script>
export default {
  name:'Curve',

  data () {
    return {
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      zoom: 3,
      center: [30, -9],
      locations: [
      {
        lat1: 40,
        lng1: -73,
        lat2: 33,
        lng2: 54
      },
      {
        lat1: 20,
        lng1: 0,
        lat2: 50,
        lng2: 40
      },
       {
        lat1: 50,
        lng1: 30,
        lat2: 10,
        lng2: 0
      }
      ],
    };
  },

  methods: {
    // this method connect two points on the map by draw a curved line 
    curve() {
      // initial map config
      const map=L.map('map').setView(this.center, this.zoom);
      L.tileLayer(this.url, { attribution: this.attribution }).addTo(map);
      
      // computational loop operator variable
      let i =0;

      const latlngs=[];

      // calculations to draw a curved line between two points
      for(const x of this.locations){

        // add marker to map for show the point of origin
        const marker1=L.marker([this.locations[i].lat1, this.locations[i].lng1]);
        marker1.addTo(map);

        // add marker to map for show the point of destination
        const marker2=L.marker([this.locations[i].lat2, this.locations[i].lng2]);
        marker2.addTo(map);

        // specify the point of origin
        const latlng1 =[this.locations[i].lat1, this.locations[i].lng1];

        // specify the point of destination
        const latlng2 =[this.locations[i].lat2, this.locations[i].lng2];

        // computational loop operator variable
        i++

        // variable for calculating longitude difference
        const offsetX= latlng2[1]-latlng1[1];

        // variable for calculating latitude difference
        const offsetY = latlng2[0]-latlng1[0];
        
        // calculations of drawing a curved line using the geometric laws of circle
        const r= Math.sqrt(Math.pow(offsetX, 2)+Math.pow(offsetY, 2)),
        theta= Math.atan2(offsetY, offsetX);

        // determine the amount of curved "Arc"
        const thetaOffset=(3.14/6);

        const r2= (r/2)/(Math.cos(thetaOffset)),
        theta2= theta+thetaOffset;
        const midpointX=(r2*Math.cos(theta2))+latlng1[1],
        midpointY= (r2*Math.sin(theta2))+latlng1[0];
        const midpointLatLng=[midpointY, midpointX];
        latlngs.push(latlng1, midpointLatLng, latlng2);

        // display styles of curved lines on the map
        const pathOptions={
          color: 'orange',
          weight: 5
        }

        // add animated motion feature to curved lines on the map
        if (typeof document.getElementById('map').animate === "function"){

          // speed of animated movements
          const durationBase=1000;

          const duration= Math.sqrt(Math.log(r))*durationBase;
          pathOptions.animate={
            duration: duration,
            iterations: Infinity,
            easing: 'ease-in-out',
            direction : 'alternate'
          }
        }

        // create a curved line holder variable using the leaflet curve method (to prepare for adding to the map)
        const curvedPath=L.curve(
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