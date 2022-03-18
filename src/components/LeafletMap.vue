<template>
  <div id="geomap" style="height: 500px"></div>
</template>

<script>
import { defineComponent, onMounted, ref, watchEffect } from "vue";
import leaflet from "leaflet";
import "leaflet/dist/leaflet.css";

export default defineComponent({
  name: "LeafletMap",
  props: {
    markers: {
      type: Array,
      required: true,
    },
  },
  setup(props) {
    let geomap;
    onMounted(() => {
      geomap = leaflet.map("geomap").setView([51.505, -0.09], 10);
      leaflet
        .tileLayer(
          "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}",
          {
            attribution:
              'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
            maxZoom: 18,
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
              "pk.eyJ1IjoiZGllZzAiLCJhIjoicWVoaUM2VSJ9.78EiZjtpunP8mAZQGXSdUg",
          }
        )
        .addTo(geomap);
    });
    watchEffect(() => {
      const myIcon = leaflet.icon({
        iconUrl: "/icons/favicon-128x128.png",
        iconSize: [50, 50],
        iconAnchor: [22, 94],
        popupAnchor: [-3, -76],
      });
      props.markers.forEach((marker) => {
        leaflet
          .marker([marker.lat, marker.lng], { icon: myIcon })
          .addTo(geomap);
      });
    });
  },
});
</script>
