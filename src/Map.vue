<template>
  <div class="wrapper">
    <gmaps-map :options="mapOptions" @mounted="ready">
      <gmaps-marker v-if="start" :position="start" />
      <gmaps-marker v-if="end" :position="end" />
    </gmaps-map>
    <input ref="autocomplete" id="autocomplete" placeholder="Search" />
  </div>
</template>

<script>
import { gmaps, gmapsMap, gmapsMarker } from "x5-gmaps";

export default {
  name: "ExampleMarkerOptions",
  components: { gmapsMap, gmapsMarker },
  data: () => ({
    mapOptions: {
      center: { lat: -27, lng: 133 },
      zoom: 4,
      fullscreenControl: false,
      mapTypeControl: false,
      rotateControl: false,
      scaleControl: false,
      streetViewControl: false,
      zoomControl: false,
    },
    // Points
    start: null,
    end: null,
    // Search data
    autocomplete: null,
    places: null,
    map: null,
    // Waypoints
    directionsService: null,
    directionsRenderer: null,
    travelMode: null,
  }),
  methods: {
    ready(map) {
      this.map = map;
      gmaps().then((maps) => {
        // Points
        this.start = new maps.LatLng(-27, 153);
        // Search
        this.places = new maps.places.PlacesService(map);
        this.autocomplete = new maps.places.Autocomplete(
          this.$refs.autocomplete
        );
        this.autocomplete.addListener("place_changed", this.update);
        // Waypoints
        this.directionsService = new maps.DirectionsService();
        this.directionsRenderer = new maps.DirectionsRenderer();
        this.directionsRenderer.setMap(map);
        this.travelMode = maps.TravelMode.DRIVING;
      });
    },
    update() {
      const place = this.autocomplete.getPlace();
      // Set end point to selected address
      if (place.geometry) {
        this.map.panTo(place.geometry.location);
        this.end = place.geometry.location;
      }
      // Display waypoints
      if (this.directionsService && this.directionsRenderer) {
        this.directionsService.route(
          {
            origin: this.start,
            destination: this.end,
            travelMode: this.travelMode,
          },
          (result, status) => {
            if (status === "OK") {
              this.directionsRenderer.setDirections(result);
            }
          }
        );
      }
    },
  },
};
</script>

<style scoped>
.wrapper {
  height: 500px;
}
#autocomplete {
  border: 3px solid rgb(255, 0, 0);
  font-size: 18px;
  height: 40px;
  left: 20%;
  padding: 0 10px;
  position: absolute;
  top: 0;
  width: 60%;
  z-index: 1;
}
</style>
