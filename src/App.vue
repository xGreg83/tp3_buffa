<template>
  <div id="app">
    <ListeDesRestaurants msg="/"/>
  </div>
</template>

<script>
import ListeDesRestaurants from './components/ListeDesRestaurants.vue'

export default {
  name: 'App',
  components: {
    ListeDesRestaurants
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

html {
  background: url('assets/new_york.jpg') no-repeat center center fixed;
}

body {
  margin: 8% !important; 
  min-height: auto !important;
}

.md-table-head-label {
  font-size: 200%;
}
</style>
