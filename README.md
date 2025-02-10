# GitHub Repository Explorer

A modern, developer-friendly web application to search and explore GitHub repositories. Built with Svelte, TypeScript, Vite, and pnpm, this single-page application lets you enter a GitHub username to retrieve public repositories, view details in a modal, and visualize language distribution with charts. The design features a dark theme with a coder/hacker vibe enhanced by JetBrains Mono.

## Features

- **Search by GitHub Username:**  
  Enter a username to fetch the user's public repositories via the GitHub API.

- **Repository Details:**  
  View repository details such as stars, forks, language, and more. Click an info icon to open a modal with extended details.

- **Sorting & Filtering:**  
  Sort repositories by stars, alphabetical order, forks, or recent updates. Filter by language using dynamic dropdowns.

- **Language Distribution Chart:**  
  Visualize the language distribution among repositories using Chart.js.

- **Modern Design & Accessibility:**  
  Designed with a dark theme, gradient backgrounds, smooth animations, and a professional coder aesthetic. Accessibility features (keyboard interactivity, ARIA roles) ensure a usable experience for everyone.

## Technologies Used

- **Svelte & SvelteKit:**  
  Lightweight, reactive framework for building modern web applications.

- **TypeScript:**  
  Ensures strong typing and better maintainability.

- **Vite:**  
  A fast build tool and development server for modern web projects.

- **pnpm:**  
  A fast, disk space–efficient package manager.

- **Chart.js:**  
  Provides interactive charts for visualizing data.

- **Netlify:**  
  Deployed using Netlify with a custom `netlify.toml` configuration.

## Installation

### Clone the Repository:

```bash
git clone https://github.com/Sylenia/GitHub-Repository-Checker
cd github-repository-explorer
```

### Install Dependencies with pnpm:

```bash
pnpm install
```

### Run the Development Server:

```bash
pnpm run dev
```

Open your browser and navigate to [http://localhost:5173](http://localhost:5173) (or the URL provided by Vite) to see the app in action.

## Project Structure

```
github-repository-explorer/
├── public/
│   └── index.html          # HTML file with custom GitHub icon and meta tags
├── src/
│   ├── lib/
│   │   ├── RepoItem.svelte  # Component for individual repository cards
│   │   ├── RepoModal.svelte # Component for displaying detailed repository info in a modal
│   │   └── LanguageChart.svelte  # Component for displaying language distribution chart (Chart.js)
│   ├── main.ts             # Application entry point
│   └── App.svelte          # Main application component
├── netlify.toml            # Netlify configuration for deployment
├── package.json
├── pnpm-lock.yaml
└── tsconfig.json
```

## Deployment

This project is configured for deployment on Netlify. The `netlify.toml` file includes:

```toml
[build]
  command = "pnpm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

### To deploy:

1. Push your Repository to GitHub.  
2. Connect your GitHub Repository to Netlify.  
3. Netlify will automatically run `pnpm run build` and publish the output from the `dist` folder.  

## Customization

### Fonts:
The project uses JetBrains Mono for a modern coder vibe. It’s imported via Google Fonts in `index.html`.

### Styling:
The dark theme is enhanced with a gradient background, subtle textures, and neon accent colors. All styles are defined in the Svelte component style blocks.

### Accessibility:
Elements with interactive behaviors include keyboard event handlers and ARIA roles for an inclusive user experience.

## Contributing

Contributions, bug reports, and feature requests are welcome! Feel free to fork the repository and create pull requests with your improvements.

## License

This project is licensed under the MIT License.

---

Happy coding! Enjoy exploring GitHub repositories with this modern, developer-friendly tool.
