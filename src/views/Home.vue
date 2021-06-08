<template>
  <div class="home">
    <h1>Places</h1><br>
    <button v-on:click="createPlace()">Create Location</button><br>
    Name: <input type="text" v-model="newPlaceParams.name"><br>
    Address: <input type="text" v-model="newPlaceParams.address"><br>
    <div v-for="place in places" v-bind:key="place.id">
      <h3>Name: {{ place.name }}</h3>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
      <dialog id="place-details">
        <form method="dialog">
          <h1>Place Info</h1>
          <p>Name: {{ place.name }}</p><br>
          Update Name: <input type="text" v-model="currentPlace.name"><br>
          <p>Address: {{ place.address }}</p>
          Update Address: <input type="text" v-model="currentPlace.address"><br>
          <button v-on:click="updatePlace()">Update</button>
          <button v-on:click="destroyPlace()">Destroy</button>
          <button>Close</button>
        </form>
      </dialog>
    </div>
  </div>
</template>

<style></style>

<script>
  import axios from "axios";
  export default {
    data: function () {
      return {
        places: [],
        newPlaceParams: {},
        currentPlace: {},
        errors: []
      };
    },
    created: function () {
      this.indexPlaces();
    },
    methods: {
      indexPlaces: function () {
        axios.get("http://localhost:3000/places")
        .then((response) => {
          console.log(response.data);
          this.places = response.data;
        })
      },
      createPlace: function () {
        axios.post("http://localhost:3000/places", this.newPlaceParams)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
          
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        })
      },
      showPlace: function (place) {
        console.log(place);
        this.currentPlace = place;
        document.querySelector("#place-details").showModal();
      },
      updatePlace: function () {
        console.log(this.currentPlace);
        var updatePlaceParams = {
          name: this.currentPlace.name,
          address: this.currentPlace.address
        };
        axios.patch(`http://localhost:3000/places/${this.currentPlace.id}`, updatePlaceParams)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        })
      },
      destroyPlace () {
        axios.delete(`http://localhost:3000/places/${this.currentPlace.id}`)
        .then((response) => {
          console.log("Deleted.", response.data);
          var index = this.places.indexOf(this.currentPlace);
          this.places.splice(index, 1);
        })
      }
    }
  };
</script>