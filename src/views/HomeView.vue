<script>
import axios from 'axios';

export default {
  data() {
    return {
      query: '',
      repositories: [],  // Array per salvare le repository
      errorMessage: null,
    };
  },
  methods: {
    async searchRepositories() {
      if (this.query.trim() === '') {
        this.errorMessage = 'Inserisci una query di ricerca';
        console.log(errorMessage);
        return;
      }
      this.errorMessage = null;
      try {
        const response = await axios.get(`https://api.github.com/search/repositories?q=${this.query}`);
        this.repositories = response.data.items;
      } catch (error) {
        this.errorMessage = error.response ? error.response.data.message : error.message;
      }
    }
  }
};
</script>

<template>
  <div class="bg-dark text-light vw-100 vh-100 d-flex justify-content-between py-4">

    <div class="mx-auto">
      <input type="text" v-model="query" placeholder="Cerca repository su GitHub">
      <button @click="searchRepositories">Cerca</button>

      <div v-if="repositories.length">
        <h3>Risultati della ricerca:</h3>
        <ul>
          <li v-for="repo in repositories" :key="repo.id">
            <a :href="repo.html_url" target="_blank">{{ repo.full_name }}</a> - {{ repo.stargazers_count }} ⭐
          </li>
        </ul>
      </div>

      <div v-if="errorMessage">
        <p class="text-danger">Error: {{ errorMessage }}</p>
      </div>
    </div>

  </div>
</template>