<template>

  <div style="height:100vh; width:100vw">
    <l-map
        :options="{scrollWheelZoom:'center'}"
        @move="moving"
        @ready="changeMapViewToCoordinates"
        v-model:zoom="zoom" v-model:center="center">
      <l-tile-layer
          attribution="aminhd2658"
          url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"/>

      <l-marker :lat-lng="coordinates"/>

      <l-control position="topleft">
        <button class="btn-my-location" @click="getUserLocation">
          <img src="./assets/myLocation.svg" alt="get current location">
        </button>
      </l-control>

    </l-map>
  </div>

</template>

<script setup>
import {LControl, LMap, LMarker, LTileLayer} from "@vue-leaflet/vue-leaflet";
import "leaflet/dist/leaflet.css"

import {onMounted, ref} from "vue";
import {latLng} from "leaflet";


const zoom = ref(18)
const center = ref(latLng(35.69970808, 51.33805453))
const coordinates = ref(latLng(35.69970808, 51.33805453))


function changeMapViewToCoordinates() {
  center.value = coordinates.value
}

function moving(e) {
  const {lat, lng} = e.target.getCenter()
  coordinates.value = latLng(lat, lng)
}


function setMarketToUserLocation(coords) {
  coordinates.value = latLng(coords.latitude, coords.longitude)
  center.value = latLng(coords.latitude, coords.longitude)
}

async function getUserLocation() {
  if ("geolocation" in navigator) {
    navigator.geolocation.getCurrentPosition(
        async position => {
          await setMarketToUserLocation(position.coords)
        },
        error => {
          console.log(error)
        },
        {
          maximumAge: Infinity,
          timeout: 5000,
          enableHighAccuracy: true,
        }
    );
  }
}

onMounted(() => {
  getUserLocation()
})

</script>

<style>
.btn-my-location {
  cursor: pointer;
  padding: 5px;
  border-radius: 100%;
  line-height: 0;
  background-color: #fff;
  border: 2px solid #ccc;
}

.btn-my-location img {
  width: 20px;
}
</style>
