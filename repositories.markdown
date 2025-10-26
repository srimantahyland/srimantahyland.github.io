---
layout: page
title: My Repositories
permalink: /repositories/
---

<div id="repositories-container">
  <p>Loading repositories...</p>
</div>

<style>
.repository {
  border: 1px solid #e1e4e8;
  border-radius: 6px;
  padding: 16px;
  margin-bottom: 16px;
  background-color: #f6f8fa;
}

.repository h3 {
  margin-top: 0;
  margin-bottom: 8px;
}

.repository h3 a {
  color: #0366d6;
  text-decoration: none;
}

.repository h3 a:hover {
  text-decoration: underline;
}

.repository-description {
  color: #586069;
  margin-bottom: 8px;
}

.repository-meta {
  font-size: 12px;
  color: #586069;
}

.repository-language {
  display: inline-block;
  margin-right: 16px;
}

.language-color {
  display: inline-block;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  margin-right: 4px;
  vertical-align: middle;
}

.repository-stats {
  display: inline-block;
  margin-right: 16px;
}

.loading {
  text-align: center;
  padding: 20px;
  color: #586069;
}

.error {
  color: #d73a49;
  padding: 16px;
  background-color: #ffeef0;
  border: 1px solid #d73a49;
  border-radius: 6px;
}
</style>

<script>
async function loadRepositories() {
  const container = document.getElementById('repositories-container');
  
  try {
    const username = 'srimantahyland';
    const response = await fetch(`https://api.github.com/users/${username}/repos?sort=updated&per_page=100`);
    
    if (!response.ok) {
      if (response.status === 403) {
        throw new Error('API rate limit exceeded. Please try again later.');
      }
      throw new Error('Failed to fetch repositories');
    }
    
    const repos = await response.json();
    
    if (repos.length === 0) {
      container.innerHTML = '<p class="loading">No repositories found.</p>';
      return;
    }
    
    // Language colors (common languages)
    const languageColors = {
      'JavaScript': '#f1e05a',
      'Python': '#3572A5',
      'Java': '#b07219',
      'Ruby': '#701516',
      'C#': '#178600',
      'HTML': '#e34c26',
      'CSS': '#563d7c',
      'Go': '#00ADD8',
      'TypeScript': '#2b7489',
      'C++': '#f34b7d',
      'C': '#555555',
      'Shell': '#89e051',
      'PHP': '#4F5D95'
    };
    
    // Sort repos by updated date (most recent first)
    repos.sort((a, b) => new Date(b.updated_at) - new Date(a.updated_at));
    
    // Helper function to escape HTML to prevent XSS
    function escapeHtml(text) {
      const div = document.createElement('div');
      div.textContent = text;
      return div.innerHTML;
    }
    
    let html = '';
    repos.forEach(repo => {
      const description = escapeHtml(repo.description || 'No description available');
      const repoName = escapeHtml(repo.name);
      const language = escapeHtml(repo.language || 'Unknown');
      const languageColor = languageColors[repo.language] || '#ccc';
      const stars = repo.stargazers_count;
      const forks = repo.forks_count;
      const updatedDate = new Date(repo.updated_at).toLocaleDateString('en-US', { 
        year: 'numeric', 
        month: 'short', 
        day: 'numeric' 
      });
      
      html += `
        <div class="repository">
          <h3><a href="${escapeHtml(repo.html_url)}" target="_blank">${repoName}</a></h3>
          <p class="repository-description">${description}</p>
          <div class="repository-meta">
            <span class="repository-language">
              <span class="language-color" style="background-color: ${escapeHtml(languageColor)};"></span>
              ${language}
            </span>
            ${stars > 0 ? `<span class="repository-stats">⭐ ${stars}</span>` : ''}
            ${forks > 0 ? `<span class="repository-stats">🍴 ${forks}</span>` : ''}
            <span>Updated on ${escapeHtml(updatedDate)}</span>
          </div>
        </div>
      `;
    });
    
    container.innerHTML = html;
  } catch (error) {
    container.innerHTML = `<p class="error">Error loading repositories: ${error.message}</p>`;
    console.error('Error:', error);
  }
}

// Load repositories when the page loads
document.addEventListener('DOMContentLoaded', loadRepositories);
</script>
