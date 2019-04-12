<template>
  <div class="products-show">
    <h2>{{ product.name }}</h2>
    <img v-bind:src="product.primary_image" v-bind:alt="product.name" />
    <p>Price: {{ product.the_total_price_is }}</p>
    <p>Description: {{ product.description }}</p>
    <p>Rating: {{ product.the_rating_is }}</p>
    <p>Number of Products: {{ product.number_of_products }}</p>
    <button v-on:click="destroyProduct(product)">Delete Product</button>

    <router-link v-bind:to="`/products/${product.id}/edit`">Edit this product | </router-link>
    <router-link to="/"> Back to all products </router-link>
    <div id="map"></div>
  </div>
</template>

<style>
#map {
  height: 300px;
  text-align: initial;
}
</style>

<script>
/* global mapboxgl */
import axios from "axios";

export default {
  data: function() {
    return {
      product: {},
      places: [
        {
          lat: 37.974728,
          long: -122.03711,
          description: "Alpine Bakery in Concord, delicious pastries!"
        },
        {
          lat: 36.973804,
          long: -122.02575,
          description: "Marinis is my favorite ice cream in Santa Cruz"
        },
        {
          lat: 37.791852,
          long: -122.42127,
          description: "Bob's Donuts in SF makes gigantic donuts, good for sharing (or eating alone!)"
        }
      ]
    };
  },
  created: function() {
    axios.get("/api/products/" + this.$route.params.id).then(response => {
      this.product = response.data;
    });
  },
  mounted: function() {
    mapboxgl.accessToken =
      "pk.eyJ1IjoianBlZWJsZXMyIiwiYSI6ImNqdWRhc2h5cTB0NzI0M25xZWZ6cThtcTYifQ.rFfoxhSgmZHr66vMqgnxfQ";

    var map = new mapboxgl.Map({
      container: "map", // container id
      style: "mapbox://styles/mapbox/streets-v11", // stylesheet location
      center: [-74.5, 40], // starting position [lng, lat]
      zoom: 5 // starting zoom
    });

    this.places.forEach(function(place) {
      var popup = new mapboxgl.Popup({ offset: 25 }).setText(place.description);
      var marker = new mapboxgl.Marker()
        .setLngLat([place.long, place.lat])
        .setPopup(popup) // sets a popup on this marker
        .addTo(map);
    });
  },

  methods: {
    destroyProduct: function(product) {
      axios.delete("/api/products/" + product.id).then(response => {
        console.log("Successfully deleted product", response.data);
        this.$router.push("/");
      });
    }
  }
};
</script>
