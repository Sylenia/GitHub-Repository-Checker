<script context="module" lang="ts">
    // Define the interface for the repository data in the module context
    export interface Repo {
      id: number;
      name: string;
      html_url: string;
      description: string | null;
      stargazers_count: number;
      forks_count: number;
      language: string | null;
      updated_at?: string;
      watchers_count?: number;
      open_issues_count?: number;
      default_branch?: string;
    }
  </script>
  
  <script lang="ts">
    // Now, import the Repo interface from the module context.
    // (It's already in the same file, so you can refer to it directly.)
    export let repo: Repo;
  
    // Accept a callback prop for when the info icon is clicked.
    // If no callback is provided, do nothing.
    export let onOpenModal: (repo: Repo) => void = () => {};
  
    function openModal() {
      onOpenModal(repo);
    }
  </script>
  
  <li class="repo-item">
    <h2>
      <a href={repo.html_url} target="_blank" rel="noopener noreferrer">
        {repo.name}
      </a>
      <!-- Info icon button to open the modal -->
      <button class="info-button" on:click={openModal} title="More info">ℹ️</button>
    </h2>
    {#if repo.description}
      <p class="description">{repo.description}</p>
    {/if}
    <p class="stats">
      {#if repo.language}
        <span class="language">{repo.language}</span> | 
      {/if}
      ⭐ {repo.stargazers_count} | 🍴 {repo.forks_count}
    </p>
  </li>
  
  <style>
    .repo-item {
      background: #2a2a2a;
      margin: 0.75rem 0;
      padding: 1rem;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
      transition: transform 0.3s ease, box-shadow 0.3s ease;
      color: #ddd;
      list-style: none;
    }
    .repo-item:hover {
      transform: scale(1.02);
      box-shadow: 0 6px 12px rgba(0, 0, 0, 0.5);
    }
    /* Header with Roboto font */
    .repo-item h2 {
      margin: 0 0 0.5rem;
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-family: 'Roboto', sans-serif;
    }
    /* Link styling with accent color */
    .repo-item a {
      color: #4fc3f7;
      text-decoration: none;
      transition: color 0.3s;
    }
    .repo-item a:hover {
      text-decoration: underline;
      color: #81d4fa;
    }
    /* Description paragraph styling */
    .repo-item .description {
      margin: 0.5rem 0;
      font-size: 0.9rem;
      line-height: 1.4;
    }
    /* Stats styling (stars, forks, language) */
    .repo-item .stats {
      margin: 0.25rem 0;
      font-size: 0.9rem;
      color: #ccc;
    }
    .language {
      font-weight: bold;
      color: #80cbc4;
    }
    /* Info button styling */
    .info-button {
      background: none;
      border: none;
      cursor: pointer;
      font-size: 1.2rem;
      color: #fff;
      transition: color 0.3s;
    }
    .info-button:hover {
      color: #81d4fa;
    }
  </style>
  