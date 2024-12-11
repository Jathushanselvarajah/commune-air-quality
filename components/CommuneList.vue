<template>
  <div>
    <!-- Barre de recherche -->
    <div class="search-bar">
      <input
        v-model="searchQuery"
        @input="filterCommunes"
        type="text"
        placeholder="Rechercher une commune..."
        class="search-input"
      />
      <i class="fas fa-search search-icon"></i>
    </div>

    <!-- Pagination -->
    <div class="pagination">
      <button
        class="pagination-button"
        :disabled="currentPage === 1"
        @click="currentPage--"
      >
        <i class="fas fa-chevron-left"></i> Précédent
      </button>
      <span>Page {{ currentPage }} / {{ totalPages }}</span>
      <button
        class="pagination-button"
        :disabled="currentPage === totalPages"
        @click="currentPage++"
      >
        Suivant <i class="fas fa-chevron-right"></i>
      </button>
    </div>

    <!-- Liste des communes filtrées -->
    <div class="communes-list">
      <div
        v-for="commune in paginatedCommunes"
        :key="commune.code"
        class="commune-card"
      >
        <p class="commune-name">{{ commune.nom }}</p>
        <button class="info-button" @click="goToDetails(commune.nom)">
          <i class="fas fa-info-circle"></i> Plus d'infos
        </button>
      </div>
    </div>

    <!-- Pagination -->
    <div class="pagination">
      <button
        class="pagination-button"
        :disabled="currentPage === 1"
        @click="currentPage--"
      >
        <i class="fas fa-chevron-left"></i> Précédent
      </button>
      <span>Page {{ currentPage }} / {{ totalPages }}</span>
      <button
        class="pagination-button"
        :disabled="currentPage === totalPages"
        @click="currentPage++"
      >
        Suivant <i class="fas fa-chevron-right"></i>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";

const communes = ref([]);
const filteredCommunes = ref([]);
const searchQuery = ref("");
const currentPage = ref(1);
const itemsPerPage = 20; // Nombre de communes par page
const router = useRouter();

const fetchCommunes = async () => {
  try {
    const response = await fetch("https://geo.api.gouv.fr/communes");
    const data = await response.json();
    communes.value = data;
    filteredCommunes.value = data;
  } catch (error) {
    console.error("Erreur lors de la récupération des communes:", error);
  }
};

const filterCommunes = () => {
  currentPage.value = 1; // Réinitialiser à la première page
  filteredCommunes.value = communes.value.filter((commune) =>
    commune.nom.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
};

const goToDetails = (communeName) => {
  router.push(`/commune/${communeName}`);
};

// Pagination logic
const totalPages = computed(() =>
  Math.ceil(filteredCommunes.value.length / itemsPerPage)
);

const paginatedCommunes = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return filteredCommunes.value.slice(start, end);
});

onMounted(fetchCommunes);
</script>

<style scoped>
/* Barre de recherche */
.search-bar {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
  position: relative;
}

.search-input {
  width: 50%;
  padding: 10px 15px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 16px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.search-icon {
  position: absolute;
  right: 26%;
  color: #007bff;
  font-size: 18px;
}

/* Liste des communes */
.communes-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  margin-top: 20px;
  padding: 0 20px;
}

.commune-card {
  padding: 20px;
  border-radius: 8px;
  border: 2px solid #e0e0e0;
  background-color: #f9f9f9;
  text-align: center;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  position: relative;
  overflow: hidden;
}

.commune-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

.commune-name {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}

.info-button {
  margin-top: 10px;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  font-size: 14px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.info-button i {
  margin-right: 5px;
}

.info-button:hover {
  background-color: #0056b3;
  transform: scale(1.05);
}

/* Pagination */
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  margin-top: 20px;
}

.pagination-button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  font-size: 14px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.pagination-button i {
  margin: 0 5px;
}

.pagination-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.pagination-button:hover:not(:disabled) {
  background-color: #0056b3;
}
</style>
