<script lang="ts">
  import RepoItem from './lib/RepoItem.svelte';
  import RepoModal from './lib/RepoModal.svelte';
  import LanguageChart from './lib/LanguageChart.svelte';
  import { fade } from 'svelte/transition';

  // Define types for repositories and the user profile.
  type Repo = {
    id: number;
    name: string;
    html_url: string;
    description: string | null;
    stargazers_count: number;
    forks_count: number;
    language: string | null;
    updated_at: string;
  };

  type UserProfile = {
    login: string;
    avatar_url: string;
    name: string;
    bio: string | null;
    public_repos: number;
  };

  // Component state variables
  let username: string = '';
  let repos: Repo[] = [];
  let profile: UserProfile | null = null;
  let loading: boolean = false;
  let error: string = '';

  // Sorting option state
  let sortOption: 'stars' | 'alphabetical' | 'forks' | 'recent' = 'stars';

  // Selected language for filtering (empty string means no filter)
  let selectedLanguage: string = "";

  // Reactive variable for unique languages
  $: languageOptions = [...new Set(repos.map(repo => repo.language).filter(Boolean))];

  // Reactive variable for filtered repositories
  $: filteredRepos = selectedLanguage 
      ? repos.filter(repo => repo.language === selectedLanguage)
      : repos;

  // State variable to store the repository selected for modal details
  let selectedRepo: Repo | null = null;

    // Compute the language distribution as an object mapping language => count.
    $: languageDistribution = repos.reduce((acc, repo) => {
    if (repo.language) {
      acc[repo.language] = (acc[repo.language] || 0) + 1;
    }
      return acc;
    }, {} as Record<string, number>);

    // Create arrays for labels and data from the distribution object.
    $: chartLabels = Object.keys(languageDistribution);
    $: chartData = chartLabels.map((label) => languageDistribution[label]);

  // Function to fetch both user profile and repositories
  async function fetchRepos() {
    error = '';
    repos = [];
    profile = null;
    selectedRepo = null;

    if (username.trim() === '') {
      error = 'Please enter a GitHub username.';
      return;
    }

    loading = true;

    try {
      // Fetch user profile
      const profileResponse = await fetch(`https://api.github.com/users/${username}`);
      if (!profileResponse.ok) {
        error =
          profileResponse.status === 404
            ? 'User not found.'
            : 'Failed to fetch profile.';
        loading = false;
        return;
      }
      profile = await profileResponse.json();

      // Fetch repositories (with per_page set to 100)
      const repoResponse = await fetch(
        `https://api.github.com/users/${username}/repos?per_page=100`
      );
      if (!repoResponse.ok) {
        error =
          repoResponse.status === 404
            ? 'Repositories not found.'
            : 'Failed to fetch repositories.';
        return;
      }
      const data: Repo[] = await repoResponse.json();
      repos = data;
      sortRepos();
    } catch (err: any) {
      error = 'Error: ' + err.message;
    } finally {
      loading = false;
    }
  }

  // Sort the repositories based on the selected option
  function sortRepos() {
    if (sortOption === 'stars') {
      repos.sort((a, b) => b.stargazers_count - a.stargazers_count);
    } else if (sortOption === 'alphabetical') {
      repos.sort((a, b) => a.name.localeCompare(b.name));
    } else if (sortOption === 'forks') {
      repos.sort((a, b) => b.forks_count - a.forks_count);
    } else if (sortOption === 'recent') {
      repos.sort(
        (a, b) => new Date(b.updated_at).getTime() - new Date(a.updated_at).getTime()
      );
    }
    // Force update by reassigning a new array reference.
    repos = [...repos];
  }

  // Handle sorting changes
  function handleSortChange(event: Event) {
    sortOption = (event.target as HTMLSelectElement).value as 'stars' | 'alphabetical' | 'forks' | 'recent';
    sortRepos();
  }
</script>

<main class="container">
  <h1>GitHub Repository Explorer</h1>
  
  <!-- Search input and button -->
  <div class="search">
    <input
      type="text"
      placeholder="Enter GitHub username"
      bind:value={username}
      on:keydown={(e) => e.key === 'Enter' && fetchRepos()}
    />
    <button on:click={fetchRepos}>Search</button>
  </div>
  
  {#if loading}
    <div class="spinner">Loading...</div>
  {:else if error}
    <p class="error">{error}</p>
  {:else if profile}
    <!-- Display user profile info -->
    <section class="profile" in:fade>
      <img src={profile.avatar_url} alt="{profile.login}'s avatar" />
      <div class="profile-details">
        <h2>{profile.name ? profile.name : profile.login}</h2>
        {#if profile.bio}
          <p>{profile.bio}</p>
        {/if}
        <p>{profile.public_repos} public repositories</p>
      </div>
    </section>

    <!-- Sorting and Filtering controls -->
    {#if repos.length > 0}
      <div class="controls">
        <div class="control-group">
          <label for="sort">Sort by:</label>
          <select id="sort" bind:value={sortOption} on:change={handleSortChange}>
            <option value="stars">Stars</option>
            <option value="alphabetical">Alphabetical</option>
            <option value="forks">Forks</option>
            <option value="recent">Recently Updated</option>
          </select>
        </div>
        <div class="control-group">
          <label for="filter">Filter by Language:</label>
          <select id="filter" bind:value={selectedLanguage}>
            <option value="">All</option>
            {#each languageOptions as lang}
              <option value={lang}>{lang}</option>
            {/each}
          </select>
        </div>
      </div>
  

    <!-- Chart Section -->
    <section class="chart-section">
      <h2>Language Distribution</h2>
        <LanguageChart labels={chartLabels} data={chartData} />
    </section>
    {/if}

    <!-- Repo list -->
    {#if filteredRepos.length === 0}
      <p>No repositories found for this filter.</p>
    {:else}
      <ul class="repo-list">
        {#each filteredRepos as repo (repo.id)}
          <li in:fade>
            <RepoItem {repo} onOpenModal={(repo) => selectedRepo = repo} />
          </li>
        {/each}
      </ul>
    {/if}
  {/if}

    <!-- Modal for detailed repository information -->
    {#if selectedRepo}
      <RepoModal repo={selectedRepo} onClose={() => selectedRepo = null} />
    {/if}
</main>

<style>
  .container {
    max-width: 800px;
    margin: 2rem auto;
    padding: 1rem;
  }
  h1 {
    text-align: center;
    color: #fff;
  }
  .search {
    display: flex;
    justify-content: center;
    margin-bottom: 1rem;
  }
  input[type="text"] {
    padding: 0.5rem;
    width: 300px;
    border: 1px solid #444;
    border-radius: 4px;
    font-size: 1rem;
    background-color: #222;
    color: #fff;
    font-family: 'JetBrains Mono', monospace;
  }
  button {
    padding: 0.5rem 1rem;
    margin-left: 0.5rem;
    border: none;
    background-color: #007BFF;
    color: white;
    border-radius: 4px;
    font-size: 1rem;
    cursor: pointer;
    transition: background-color 0.2s ease-in-out;
  }
  button:hover {
    background-color: #0056b3;
  }
  .spinner {
    text-align: center;
    font-size: 1.2rem;
    margin: 2rem 0;
  }
  .error {
    color: red;
    text-align: center;
  }
  .profile {
    display: flex;
    align-items: center;
    margin-bottom: 1rem;
    padding: 1rem;
    border: 1px solid #444;
    border-radius: 8px;
    background-color: #222;
  }
  .profile img {
    width: 80px;
    height: 80px;
    border-radius: 50%;
    margin-right: 1rem;
  }
  .profile-details {
    flex: 1;
  }
  .controls {
    margin: 1rem 0;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .control-group {
    display: inline-block;
    margin-right: 1rem;
  }
  .control-group label {
    font-family: 'JetBrains Mono', monospace;
  }
  .repo-list {
    list-style: none;
    padding: 0;
  }

  .chart-section {
    margin: 2rem 0;
    text-align: center;
  }

  .chart-section h2 {
    margin-bottom: 1rem;
  }
</style>
