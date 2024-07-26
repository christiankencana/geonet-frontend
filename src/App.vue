<template>
  <!-- <div class="container-fluid p-0"> -->
  <div class="d-flex flex-column vh-100">
    <Navbar />
    <div class="container-fluid flex-grow-1">
      <div class="row h-100">
        <!-- <Sidebar /> -->
        <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4 my-3 content-wrapper">
          <router-view></router-view>
        </main>
      </div>
    </div>
  </div>
</template>

<script setup>
import Navbar from './components/Navbar.vue'
import Sidebar from './components/Sidebar.vue'
</script>

<style>
.content-wrapper {
  margin-top: 56px;
}
</style>