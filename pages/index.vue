<template>
  <div class="container">
    <h1 class="title">Liste des Communes</h1>

    <CommuneList />
  </div>
</template>

<script setup>
import CommuneList from "./components/CommuneList.vue";
</script>

<style scoped>
.container {
  max-width: 1200px;
  margin: auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.title {
  text-align: center;
  color: #333;
}
</style>
