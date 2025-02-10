<script lang="ts">
    // Define the repository type
    export let repo: {
      id: number;
      name: string;
      html_url: string;
      description: string | null;
      stargazers_count: number;
      forks_count: number;
      language: string | null;
      updated_at: string;
      watchers_count?: number;
      open_issues_count?: number;
      default_branch?: string;
    };
  
    // Accept a callback prop for closing the modal.
    // If not provided, default to an empty function.
    export let onClose: () => void = () => {};
  
    function close() {
      onClose();
    }
  </script>
  
  <div 
    class="modal-backdrop" 
    role="button"
    tabindex="0"
    on:click={close}
    on:keydown={(e) => {
        if (e.key === 'Enter' || e.key === ' ') {
          close();
        }
    }}>
    <div 
        class="modal-content" 
        role="presentation" 
        on:click|stopPropagation 
        on:keydown={(e) => e.stopPropagation()}
        >
      <button class="close-button" on:click={close}>×</button>
      <h2>{repo.name} Details</h2>
      {#if repo.description}
        <p>{repo.description}</p>
      {/if}
      <ul>
        <li><strong>Stars:</strong> {repo.stargazers_count}</li>
        <li><strong>Forks:</strong> {repo.forks_count}</li>
        {#if repo.language}
          <li><strong>Language:</strong> {repo.language}</li>
        {/if}
        {#if repo.watchers_count !== undefined}
          <li><strong>Watchers:</strong> {repo.watchers_count}</li>
        {/if}
        {#if repo.open_issues_count !== undefined}
          <li><strong>Open Issues:</strong> {repo.open_issues_count}</li>
        {/if}
        {#if repo.default_branch}
          <li><strong>Default Branch:</strong> {repo.default_branch}</li>
        {/if}
        <li><strong>Last Updated:</strong> {new Date(repo.updated_at).toLocaleString()}</li>
      </ul>
      <p>
        <a href={repo.html_url} target="_blank" rel="noopener noreferrer">
          View on GitHub
        </a>
      </p>
    </div>
  </div>
  
  <style>
    /* Modal Backdrop */
    .modal-backdrop {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: rgba(0, 0, 0, 0.8);
      display: flex;
      align-items: center;
      justify-content: center;
      z-index: 1000;
      animation: fadeIn 0.3s ease-out;
    }
    /* Modal Content */
    .modal-content {
      background: #2a2a2a;
      color: #eee;
      padding: 2rem;
      border-radius: 8px;
      max-width: 500px;
      width: 90%;
      position: relative;
      box-shadow: 0 6px 12px rgba(0, 0, 0, 0.6);
      animation: slideIn 0.3s ease-out;
    }
    /* Close Button */
    .close-button {
      position: absolute;
      top: 0.5rem;
      right: 0.5rem;
      background: transparent;
      border: none;
      font-size: 1.5rem;
      color: #eee;
      cursor: pointer;
      transition: color 0.3s;
    }
    .close-button:hover {
      color: #81d4fa;
    }
    /* Headings & Text */
    h2 {
      margin-top: 0;
      font-family: 'JetBrains Mono', monospace;
    }
    p {
      line-height: 1.5;
      font-size: 0.95rem;
    }
    ul {
      padding-left: 1.5rem;
      font-size: 0.9rem;
      margin: 1rem 0;
    }
    li {
      margin-bottom: 0.5rem;
    }
    a {
      color: #4fc3f7;
      text-decoration: none;
      transition: color 0.3s;
    }
    a:hover {
      color: #81d4fa;
      text-decoration: underline;
    }
    /* Animations */
    @keyframes fadeIn {
      from { opacity: 0; }
      to   { opacity: 1; }
    }
    @keyframes slideIn {
      from { transform: translateY(-20px); opacity: 0; }
      to   { transform: translateY(0); opacity: 1; }
    }
  </style>
  