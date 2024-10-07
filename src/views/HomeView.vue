<script>
export default {
  data() {
    return {
      searchQuery: '',
      searchType: 'repositories', // Tipo di ricerca predefinito
      results: [],
      isLoading: false, // Per gestire il loader
      errorMessage: ''
    };
  },
  methods: {
    async performSearch() {
      // Validazione: almeno 3 caratteri nella query
      if (this.searchQuery.length < 3) {
        this.errorMessage = "Please enter at least 3 characters to search.";
        this.results = [];
        return;
      }

      this.isLoading = true; // Mostra loader
      this.errorMessage = ''; // Resetta eventuali messaggi di errore

      const baseUrl = this.searchType === 'repositories'
        ? 'https://api.github.com/search/repositories?q='
        : 'https://api.github.com/search/users?q=';

      const url = `${baseUrl}${this.searchQuery}`;

      try {
        const response = await fetch(url);
        const data = await response.json();

        // Se non ci sono risultati
        if (data.items.length === 0) {
          this.errorMessage = "No results found.";
        }
        this.results = data.items;
      } catch (error) {
        this.errorMessage = "Error fetching data from GitHub API.";
        console.error(error);
      } finally {
        this.isLoading = false; // Nasconde il loader
      }
    }
  }
};
</script>

<template>
  <div class="w-100 min-vh-100 bg-dark text-light p-5">
    <h1 class="text-center">GitHub Search</h1>

    <!-- Barra di ricerca -->
    <div class="row mt-4 mb-4">
      <div class="col-md-6 offset-md-3">
        <div class="input-group">
          <input v-model="searchQuery" type="text" class="form-control" placeholder="Search GitHub"
            aria-label="Search GitHub" />
          <button @click="performSearch" class="btn btn-primary">
            Search
          </button>
        </div>
      </div>
    </div>

    <!-- Dropdown per scegliere il tipo di ricerca -->
    <div class="row mt-4">
      <div class="col-md-6 offset-md-3">
        <select v-model="searchType" class="form-select mb-3">
          <option value="repositories">Repositories</option>
          <option value="users">Users/Organizations</option>
        </select>
      </div>
    </div>

    <!-- Loader -->
    <div v-if="isLoading" class="text-center mt-3">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>

    <!-- Messaggio di errore o mancanza di risultati -->
    <div v-if="errorMessage" class="text-center text-danger mt-3">
      {{ errorMessage }}
    </div>

    <!-- Risultati della ricerca -->
    <div class="row">
      <div class="w-100 d-flex justify-content-center">
        <div class="row w-75">
          <div v-for="result in results" :key="result.id" class="col-md-4 mb-4">
            <div class="card">
              <div class="card-body m-auto text-center">
                <!-- Card per repositories -->
                <div v-if="searchType === 'repositories'">
                  <h5 class="card-title">{{ result.name }}</h5>
                  <p class="card-text">Owner: {{ result.owner.login }}</p>
                  <a :href="result.html_url" target="_blank" class="btn btn-primary">View Repository</a>
                </div>

                
                <!-- Card per utenti/organizzazioni -->
                <div v-else>
                  <h5 class="card-title">{{ result.login }}</h5>
                  <img :src="result.avatar_url" class="img-fluid rounded-circle mb-2 h-50 w-50 d-block m-auto" />
                  <a :href="result.html_url" target="_blank" class="btn btn-primary">View Profile</a>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>
