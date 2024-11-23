<template>
  <div class="commune-details">
    <!-- Retour Button -->
    <div class="back-button">
      <router-link to="#" @click.prevent="goBack">
        <i class="fas fa-arrow-left"></i> Retour
      </router-link>
    </div>

    <h1>
      <i class="fas fa-city"></i> Détails de la Commune : {{ communeName }}
    </h1>

    <!-- Détails de la commune -->
    <div v-if="communeDetails" class="details-section">
      <h2><i class="fas fa-info-circle"></i> Informations sur la Commune</h2>
      <p>
        <strong
          ><i class="fas fa-map-marker-alt"></i> Code Département :</strong
        >
        {{ communeDetails.codeDepartement }}
      </p>
      <p>
        <strong><i class="fas fa-envelope"></i> Code postal :</strong>
        {{ communeDetails.codesPostaux.join(", ") }}
      </p>
      <p>
        <strong><i class="fas fa-users"></i> Population :</strong>
        {{ communeDetails.population }}
      </p>
      <p>
        <strong><i class="fas fa-map"></i> Région :</strong>
        {{ communeDetails.codeRegion }}
      </p>
    </div>

    <!-- Qualité de l'air -->
    <div class="air-quality-section">
      <h2><i class="fas fa-wind"></i> Qualité de l'Air</h2>
      <div v-if="airQuality">
        <p>
          <strong
            ><i class="fas fa-smog"></i> AQI (Indice qualité de l'air) :</strong
          >
          {{ airQuality.aqi }}
        </p>
        <p>
          <strong><i class="fas fa-seedling"></i> PM2.5 :</strong>
          {{ airQuality.iaqi.pm25.v }} µg/m³
        </p>
        <p>
          <strong><i class="fas fa-cloud"></i> PM10 :</strong>
          {{ airQuality.iaqi.pm10.v }} µg/m³
        </p>
        <p>
          <strong><i class="fas fa-sun"></i> Ozone (O3) :</strong>
          {{ airQuality.iaqi.o3.v }} µg/m³
        </p>
        <p>
          <strong><i class="fas fa-thermometer-half"></i> Température :</strong>
          {{ airQuality.iaqi.t.v }} °C
        </p>
        <p>
          <strong><i class="fas fa-tint"></i> Humidité :</strong>
          {{ airQuality.iaqi.h.v }} %
        </p>
        <p>
          <strong><i class="fas fa-wind"></i> Vitesse du vent :</strong>
          {{ airQuality.iaqi.w.v }} km/h
        </p>
      </div>
      <div v-if="!airQuality" class="no-info">
        <i class="fas fa-exclamation-circle"></i> Pas d'infos disponibles pour
        la ville
      </div>
    </div>

    <!-- Carte Leaflet -->
    <div v-if="communeDetails" class="map-section">
      <h2><i class="fas fa-map"></i> Carte</h2>
      <l-map :zoom="zoom" :center="center" style="height: 500px; width: 100%">
        <l-tile-layer :url="url" :attribution="attribution"></l-tile-layer>
        <l-marker :lat-lng="center"></l-marker>
      </l-map>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import { LMap, LTileLayer, LMarker } from "@vue-leaflet/vue-leaflet";
import "leaflet/dist/leaflet.css";

const route = useRoute();
const router = useRouter();
const communeName = route.params.name; // Fetch the commune name from URL params
const communeDetails = ref(null);
const airQuality = ref(null);
const loading = ref(true);
const error = ref(false);
const zoom = ref(13);
const center = ref([0, 0]); // Default center
const url = "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png";
const attribution =
  '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors';

// Fetch the details of the commune based on its name
const fetchCommuneDetails = async () => {
  try {
    // Requête pour obtenir les détails de la commune
    const response = await fetch(
      `https://geo.api.gouv.fr/communes?nom=${communeName}`
    );
    const data = await response.json();
    console.log("Données reçues de l'API : ", data);

    if (data.length > 0) {
      communeDetails.value = data[0];
      const communeCode = communeDetails.value.code;

      // Requête pour obtenir les coordonnées de la commune
      const responseWithCoords = await fetch(
        `https://geo.api.gouv.fr/communes/${communeCode}?fields=centre`
      );
      const dataWithCoords = await responseWithCoords.json();
      console.log("Détails de la commune avec coordonnées : ", dataWithCoords);

      // Vérifier que les coordonnées sont présentes
      if (
        dataWithCoords &&
        dataWithCoords.centre &&
        dataWithCoords.centre.coordinates
      ) {
        const coordinates = dataWithCoords.centre.coordinates;
        console.log("Coordonnées récupérées : ", coordinates);

        if (coordinates.length === 2) {
          const [lon, lat] = coordinates; // Longitude, Latitude
          console.log("Latitude et Longitude récupérées : ", lat, lon);
          center.value = [lat, lon]; // Mise à jour du centre de la carte
        } else {
          throw new Error("Coordonnées invalides");
        }
      } else {
        throw new Error("Aucune coordonnée trouvée");
      }
    } else {
      throw new Error("Aucune commune trouvée");
    }
  } catch (err) {
    console.error("Erreur lors de la récupération des détails:", err);
    error.value = true;
  }
};

// Fetch the air quality data for the commune
const fetchAirQuality = async () => {
  try {
    const response = await fetch(
      `https://api.waqi.info/feed/${communeName}/?token=8c60735638c56ca1699b92ac4975cd64891a87f5`
    );
    const data = await response.json();
    if (data.data) {
      airQuality.value = data.data;
    } else {
      throw new Error("Données de qualité de l'air non disponibles");
    }
  } catch (err) {
    console.error(
      "Erreur lors de la récupération de la qualité de l'air:",
      err
    );
    airQuality.value = null;
    error.value = true;
  }
};

// Navigate back to the previous page
const goBack = () => {
  router.go(-1);
};

// Fetch data on component mount
onMounted(() => {
  fetchCommuneDetails();
  fetchAirQuality();
  loading.value = false;
});
</script>

<style scoped>
@import url("https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css");

.commune-details {
  padding: 20px;
  font-family: Arial, sans-serif;
  max-width: 800px;
  margin: 0 auto;
  background: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h1 {
  font-size: 24px;
  color: #333;
  margin-bottom: 20px;
  text-align: center;
}

h2 {
  font-size: 20px;
  color: #555;
  margin-bottom: 15px;
}

p {
  font-size: 16px;
  margin: 5px 0;
  color: #333;
}

strong {
  color: #007bff;
}

.error {
  color: #d9534f;
  font-weight: bold;
  margin-top: 20px;
}

.no-info {
  color: #555;
  font-style: italic;
  margin-top: 10px;
}

.loading {
  color: #007bff;
  text-align: center;
  font-size: 18px;
  margin-top: 20px;
}

.map-section {
  margin-top: 20px;
}

.back-button a {
  text-decoration: none; /* Removes underline */
  color: inherit; /* Keeps the text color the same as the surrounding text */
}

.back-button a:hover {
  color: #007bff; /* Changes the text color to blue on hover */
}

.back-button {
  margin-bottom: 20px;
  font-size: 16px;
}

.back-button i {
  margin-right: 8px;
  font-size: 18px;
}
</style>
