<script>
export default {
  data() {
    return {
      searchQuery: '',
      searchType: 'repositories', // Default search type
      results: []
    };
  },
  methods: {
    async performSearch() {
      const baseUrl = this.searchType === 'repositories' ? 'https://api.github.com/search/repositories?q=' : 'https://api.github.com/search/users?q=';
      const url = `${baseUrl}${this.searchQuery}`;

      try {
        const response = await fetch(url);
        const data = await response.json();
        this.results = this.searchType === 'repositories' ? data.items : data.items;
      } catch (error) {
        console.error("Error fetching data from GitHub API", error);
      }
    }
  }
};
</script>


<template>
  <div class="w-100 vh-100 bg-dark text-light p-5">
    <h1 class="text-center">GitHub Search</h1>

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

    <div class="row mt-4">
      <div class="col-md-6 offset-md-3">
        <select v-model="searchType" class="form-select mb-3">
          <option value="repositories">Repositories</option>
          <option value="users">Users/Organizations</option>
        </select>
      </div>
    </div>

    <div class="w-100 d-flex justify-content-center">
      <div class="row w-75">
        <div v-if="results.length === 0" class="text-center">
          No results found.
        </div>

        <div v-for="result in results" :key="result.id" class="col-md-4 mb-4">
          <div class="card">
            <div class="card-body m-auto text-center">
              <!-- Card for repositories -->
              <div v-if="searchType === 'repositories'">
                <h5 class="card-title">{{ result.name }}</h5>
                <p class="card-text">Owner: {{ result.owner.login }}</p>
                <a :href="result.html_url" target="_blank" class="btn btn-primary">View Repository</a>
              </div>
              <!-- Card for users/organizations -->
              <div v-else>
                <h5 class="card-title">{{ result.login }}</h5>
                <img :src="result.avatar_url" class="img-fluid rounded-circle mb-2" />
                <a :href="result.html_url" target="_blank" class="btn btn-primary">View Profile</a>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>
