<template>
  <div id="app">
    <h1>Rick and Morty</h1>
    <div>
      <input v-model="filters.name" placeholder="Filter by name" />
      <select v-model="filters.status">
        <option value="">All statuses</option>
        <option value="alive">Alive</option>
        <option value="dead">Dead</option>
        <option value="unknown">Unknown</option>
      </select>
      <button @click="applyFilters">Применить</button>
    </div>
    <div class="characters">
      <div
        v-for="character in characters"
        :key="character.id"
        class="character-card"
      >
        <img :src="character.image" :alt="character.name" />
        <h2>{{ character.name }}</h2>
        <p>Status: {{ character.status }}</p>
      </div>
    </div>
    <div class="pagination">
      <button @click="prevPage" :disabled="page === 1">{{ "<" }}</button>
      <span>Page {{ page }}</span>
      <button @click="nextPage" :disabled="page === totalPages">
        {{ ">" }}
      </button>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {
  setup() {
    const characters = ref([]);
    const page = ref(1);
    const totalPages = ref(1);
    const filters = ref({
      name: "",
      status: "",
    });

    const fetchCharacters = async () => {
      const response = await fetch(
        `https://rickandmortyapi.com/api/character/?page=${page.value}&name=${filters.value.name}&status=${filters.value.status}`
      );
      const data = await response.json();
      characters.value = data.results;
      totalPages.value = data.info.pages;
    };

    const applyFilters = () => {
      page.value = 1;
      fetchCharacters();
    };

    const nextPage = () => {
      if (page.value < totalPages.value) {
        page.value++;
        fetchCharacters();
      }
    };

    const prevPage = () => {
      if (page.value > 1) {
        page.value--;
        fetchCharacters();
      }
    };

    onMounted(fetchCharacters);

    return {
      characters,
      page,
      totalPages,
      filters,
      applyFilters,
      nextPage,
      prevPage,
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 50px;
}

.characters {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.character-card {
  border: 1px solid #ddd;
  border-radius: 10px;
  padding: 10px;
  margin: 10px;
  width: 200px;
  text-align: center;
}

.character-card img {
  max-width: 100%;
  border-radius: 50%;
}

.pagination {
  margin-top: 20px;
}
</style>
