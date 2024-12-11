<template>
  <div class="homepage">
    <header class="hero">
      <h1 class="hero-title">Bienvenue sur <span>Commune Details</span></h1>
      <p class="hero-subtitle">
        Découvrez les communes de France avec leurs informations clés et une
        carte interactive pour une navigation fluide. 🌍📍
      </p>
      <button class="cta-button" @click="goToCommunes">
        Découvrir les Communes
      </button>
    </header>
  </div>
</template>

<script setup>
import { useRouter } from "vue-router";
const router = useRouter();

const goToCommunes = () => {
  router.push("/commune");
};
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Fixe la hauteur et la largeur de la page */
html,
body {
  height: 100%;
  width: 100%;
}
/* General Styles */
.homepage {
  font-family: "Arial", sans-serif;
  text-align: center;
  color: #333;
  background: linear-gradient(to bottom right, #007bff, #0056b3);
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  box-sizing: border-box;
}

/* Hero Section */
.hero {
  background-color: #fff;
  padding: 40px 20px;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  max-width: 600px;
  width: 100%;
}

.hero-title {
  font-size: 2.5rem;
  margin-bottom: 20px;
}

.hero-title span {
  color: #007bff;
}

.hero-subtitle {
  font-size: 1.2rem;
  margin-bottom: 30px;
}

.cta-button {
  padding: 15px 30px;
  font-size: 1rem;
  color: #fff;
  background-color: #007bff;
  border: none;
  border-radius: 25px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.cta-button:hover {
  background-color: #0056b3;
  transform: scale(1.05);
}
</style>
