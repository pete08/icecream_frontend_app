<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <!-- <button v-on:click="indexFlavor()">Here show all Flavors</button> -->

    <div>
      <p> name: <input type="text" v-model="newname"> </p>
      <p> ingredients: <input type="text" v-model="newingredients"> </p>
      <p> image_url: <input type="text" v-model="newimage_url"> </p>
      <button v-on:click="createFlavor()">create a new flavor!!</button>
      <hr>
    </div>
  

    <div v-for="flavor in flavors">
      <br>
      <p>name: {{flavor.name}}</p>
      <p>ingredients: {{flavor.ingredients}}</p>
      <img v-bind:src="flavor.image_url" style= "width: 300px;">
      <br>
      <button v-on:click="showFlavor(flavor)">Find out More!</button>
      <hr>
    </div>

    <dialog id="flavor-detail">
      <form method="dialog">
        <p> name: <input type="text" v-model="currentFlavor.name"> </p>
        <p> ingredients: <input type="text" v-model="currentFlavor.ingredients">  </p>
        <p> image_url: <input type="text" v-model="currentFlavor.image_url">  </p>
        <p> price: $5.00/scoop </p>
        <button v-on:click="updateFlavor()">update flavor</button>
        ||
        <button v-on:click="destroyFlavor()">delete!</button>
        ||
        <button> close </button>
      </form>
    </dialog>

  </div>
</template>

<style>
</style>

<script>
import axios from 'axios' ;
export default {
  data: function() {
    return {
      message: "Welcome to the wonderful worlld of icecream!",
      flavors: [],
      currentFlavor: {},
      newname: "",
      newingredients: "",
      newimage_url: "",
      flavor: "",
    };
  },
  
  created: function() {
    this.indexFlavor();
  },
  
  methods: {
    indexFlavor: function() {
      axios.get("/api/flavors").then(response => {
        console.log("Flavors index action: ", response);
        this.flavors = response.data;
      }) ;
    },
    showFlavor: function(flavor) {
      this.currentFlavor = flavor ;
      console.log("show flavor detail")
      document.querySelector("#flavor-detail").showModal();
    },

    createFlavor: function() {
      var params = {
        name: this.newname,
        ingredients: this.newingredients,
        image_url: this.newimage_url
      };
      axios.post("/api/flavors", params).then(response => {
        console.log("Flavors Create action: ", response);
        this.flavors.push(response.data);
      });
    },

    updateFlavor: function() {
      var params = {
        name: this.currentFlavor.name,
        ingredients: this.currentFlavor.ingredients,
        image_url: this.currentFlavor.image_url
      };
      console.log("hellow from updateFlavor")
      
      axios
        .patch("/api/flavors/" + this.currentFlavor.id, params)
        .then(response => {
          console.log("Updated flavor to: ", response);
          this.currentFlavor = {};
          this.indexFlavor();
        });
    },

    destroyFlavor: function() {
      console.log("hellow from destroyFlavor")
      axios
        .delete("/api/flavors/" + this.currentFlavor.id)
        .then(response => {
          console.log("successfull deleted flavor!: " + this.currentFlavor.name) ;
          var index = this.flavors.indexOf(this.currentFlavor);
          this.flavors.splice(index, 1);
        });
    }


  }
};
</script>
