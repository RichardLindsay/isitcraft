<template>
  <div id="app">
    <input type="text" v-model="search_term" v-on:keyup="search_breweries" />
    <ul v-if="potential_breweries.length">
      <li v-for="potential_brewery in potential_breweries" v-bind:key="potential_brewery">
        <a
          v-bind:href="potential_brewery.id"
          v-on:click.prevent="check_brewery(potential_brewery.id)"
        >{{potential_brewery.name}}</a>
      </li>
    </ul>
    <div v-if="Object.keys(selected_brewery).length">
      <p v-if="this.selected_brewery.brewery_type == 'large'">No</p>
      <p v-else>Yes</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      search_term: "",
      potential_breweries: [],
      selected_brewery: {}
    };
  },
  methods: {
    search_breweries: function() {
      if (this.search_term.length > 2) {
        fetch(
          `https://api.openbrewerydb.org/breweries/autocomplete?query=${this.search_term}`
        )
          .then(response => response.json())
          .then(data => {
            this.potential_breweries = data;
          });
      } else if (this.search_term.length == 0) {
        this.potential_breweries = [];
        this.selected_brewery = {};
      }
    },
    check_brewery: function(brewery_id) {
      fetch(`https://api.openbrewerydb.org/breweries/${brewery_id}`)
        .then(response => response.json())
        .then(data => {
          this.selected_brewery = data;
          this.potential_breweries = [];
          this.search_term = this.selected_brewery.name;
        });
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

input {
  height: 60px;
  padding: 0 30px;
  width: 30vw;
  border: 2px solid grey;
  border-radius: 5px;
  font-size: 26px;
}
</style>
