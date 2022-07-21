<template>
  <div id="mapContainer"></div>
</template>

<script setup lang="ts">
import { onMounted } from 'vue'

import "leaflet/dist/leaflet.css";
import L from "leaflet";


onMounted(() => {



  let map: L.Map | L.LayerGroup<any>;
  let geojson;
  let gooUrl: string = "https://{s}.google.com/vt/lyrs=s,h&x={x}&y={y}&z={z}";
  let gooAttrib: string = "Map data &copy; Google Maps";

  var myStyle = {
    "color": "#009efd",
    "weight": 2,
    "opacity": 0.65
  };

  map = L.map("mapContainer").setView([-26.52537, -53.74268], 12);

  L.tileLayer(gooUrl, {
    maxZoom: 23,
    subdomains: ["mt0", "mt1", "mt2", "mt3"],
    attribution: gooAttrib,
  }).addTo(map);

  // FETCH PARCELAS
  const onFetch = async () => {
    const res = await fetch("https://development.gis.gbmland.io/api/pg2gj?geotable=zonas_hex37mi&geomfield=the_geom_webmercator&fields=gid,area&parameters=gid = 1 or gid = 2");
    const resToJson = await res.json();
    console.log(JSON.stringify(resToJson, null,3))

    L.geoJSON(resToJson, {
      style: myStyle
    }).addTo(map);

  }
  onFetch()

  // -----------------------------
  // -----------------------------
  // -----------------------------
  // handling map coords
  function onMapClick(e: any) {
    const coords: any = e.latlng
    const { lat, lng } = coords


// showing popup
    L.popup()
      .setLatLng([lat, lng ])
      .setContent(`Lat: ${lat} <br /> Long: ${lng}`)
      .openOn(map as any);
  }

  map.on('click', onMapClick);

  // hexagon-- draw MultiPolygon
  // ------------------------

})

</script>

<style scoped>
#mapContainer {
  width: 100vw;
  height: 100vh;
}
</style>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
</style>
