<script setup>
import { RouterView } from 'vue-router'
import MainNavbar from './components/Navbar.vue'
</script>

<template>
  <div class="container-fluid">
    <div class="row" id="navbar-container">
      <MainNavbar />
    </div>
    <div class="row">
      <RouterView />
    </div>
  </div>
</template>
