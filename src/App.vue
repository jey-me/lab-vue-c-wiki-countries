<!-- src/App.vue -->
<template>
  <div class="app">
    <Navbar />
    <div class="container">
        <router-view  />
  
    </div>
  </div>
</template>

<script setup>
import Navbar from "./components/Navbar.vue";
import CountriesList from "./components/CountriesList.vue";
</script>

<style>

</style>
