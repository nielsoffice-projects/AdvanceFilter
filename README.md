# AdvanceFilter
HTML, CSS and JS 


```CSS

  .controls {
    margin-bottom: 20px;
  }
  .controls > * {
    margin-right: 15px;
  }
  .filters {
    margin-top: 10px;
  }
  .filters > div {
    margin-bottom: 10px;
  }
  #postsContainer {
    display: grid;
    gap: 20px;
  }
  /* Default is list view */
  #postsContainer.list-view {
    display: block;
  }
  /* Card style for each post */
  .post {
    border: 1px solid #ddd;
    padding: 15px;
    border-radius: 5px;
    display: flex;
    gap: 15px;
  }
  .post img {
    width: 120px;
    height: 90px;
    object-fit: cover;
    border-radius: 4px;
    flex-shrink: 0;
  }
  .post-content {
    flex-grow: 1;
  }
  .post-title {
    font-weight: bold;
    margin-bottom: 5px;
  }
  .post-description {
    font-size: 0.9em;
    margin-bottom: 8px;
  }
  .post-meta {
    font-size: 0.8em;
    color: #666;
  }

  /* Grid (column) view styles */
  #postsContainer.grid-view {
    display: grid;
  }
  /* Responsive grid columns for grid view */
  @media (min-width: 1024px) {
    #postsContainer.grid-view {
      grid-template-columns: repeat(4, 1fr);
    }
  }
  @media (min-width: 600px) and (max-width: 1023px) {
    #postsContainer.grid-view {
      grid-template-columns: repeat(2, 1fr);
    }
  }
  @media (max-width: 599px) {
    #postsContainer.grid-view {
      grid-template-columns: 1fr;
    }
  }
  /* In grid view, make post display block */
  #postsContainer.grid-view .post {
    flex-direction: column;
    align-items: flex-start;
  }
  #postsContainer.grid-view .post img {
    width: 100%;
    height: 150px;
  }
  /* Hide and show filters */
  .hidden {
    display: none;
  }
  /* Load more button style */
  #loadMoreBtn {
    margin: 20px auto;
    display: block;
    padding: 10px 20px;
    font-size: 1em;
    cursor: pointer;
  }

```

```HTML


<div class="controls">
  <button id="listViewBtn">List View</button>
  <button id="gridViewBtn">Grid View</button>
</div>

<div class="filters">
  <div>
    <input type="text" id="searchInput" placeholder="Search by title or author" />
  </div>
  <div>
    <label><input type="checkbox" id="advanceCheck" /> Advance</label>
  </div>
  <div id="advanceFilters" class="hidden">
    <div>
      <label>From Date: <input type="date" id="fromDate" /></label>
      <label>To Date: <input type="date" id="toDate" /></label>
    </div>
    <div>
      <label><input type="checkbox" id="filterTag" /> Filter by Tag</label>
      <label><input type="checkbox" id="filterTitle" /> Filter by Title</label>
      <label><input type="checkbox" id="filterCategory" /> Filter by Category</label>
    </div>
  </div>
</div>

<div id="postsContainer" class="list-view">
  <!-- Posts will render here -->
</div>

<button id="loadMoreBtn">Load More</button>


```


```JS

// Sample JSON data with 6 posts
const data = [
  {
    "title": "Exploring the Future of AI Technology",
    "description": "Artificial intelligence is shaping the future in unprecedented ways. From automation to decision-making, its potential is limitless.",
    "date_post": "2025-08-05T14:12:30",
    "author": "Emma Thompson",
    "tags": ["tech", "education", "life"],
    "feature_image": "https://placeimg.com/640/480/tech",
    "visited_number": 482,
    "views_number": 1893
  },
  {
    "title": "10 Tips for Healthy Eating on a Budget",
    "description": "Eating healthy doesn’t have to be expensive. Here are ten practical ways to stick to a nutritious diet without breaking the bank.",
    "date_post": "2025-06-19T09:45:12",
    "author": "Liam Smith",
    "tags": ["health", "food", "finance"],
    "feature_image": "https://placeimg.com/640/480/food",
    "visited_number": 245,
    "views_number": 1175
  },
  {
    "title": "How to Travel the World Safely in 2025",
    "description": "Traveling has changed, but the wanderlust lives on. Learn the best tips for safe, affordable, and memorable travel this year.",
    "date_post": "2025-02-11T18:32:05",
    "author": "Sophia Lee",
    "tags": ["travel", "life", "education"],
    "feature_image": "https://placeimg.com/640/480/travel",
    "visited_number": 392,
    "views_number": 2143
  },
  {
    "title": "Mastering the Art of Remote Work",
    "description": "Remote work is here to stay. Discover tools, techniques, and mindsets to stay productive and connected.",
    "date_post": "2025-07-20T11:15:00",
    "author": "Olivia Brown",
    "tags": ["work", "tech", "life"],
    "feature_image": "https://placeimg.com/640/480/arch",
    "visited_number": 365,
    "views_number": 1350
  },
  {
    "title": "The Rise of Electric Vehicles",
    "description": "Electric vehicles are revolutionizing transportation. Explore the benefits and challenges ahead.",
    "date_post": "2025-03-15T08:00:00",
    "author": "Noah Davis",
    "tags": ["tech", "environment", "auto"],
    "feature_image": "https://placeimg.com/640/480/auto",
    "visited_number": 400,
    "views_number": 1900
  },
  {
    "title": "Mindfulness and Mental Health in the Digital Age",
    "description": "How mindfulness practices can improve mental well-being in a world dominated by screens and constant connection.",
    "date_post": "2025-04-25T13:45:00",
    "author": "Ava Wilson",
    "tags": ["health", "mindfulness", "life"],
    "feature_image": "https://placeimg.com/640/480/nature",
    "visited_number": 280,
    "views_number": 1100
  }
];

// Pagination state
const POSTS_PER_LOAD = 3;
let currentIndex = 0;
let filteredData = [...data]; // start with full data

// DOM elements
const postsContainer = document.getElementById('postsContainer');
const listViewBtn = document.getElementById('listViewBtn');
const gridViewBtn = document.getElementById('gridViewBtn');
const searchInput = document.getElementById('searchInput');
const advanceCheck = document.getElementById('advanceCheck');
const advanceFilters = document.getElementById('advanceFilters');
const fromDateInput = document.getElementById('fromDate');
const toDateInput = document.getElementById('toDate');
const filterTag = document.getElementById('filterTag');
const filterTitle = document.getElementById('filterTitle');
const filterCategory = document.getElementById('filterCategory');
const loadMoreBtn = document.getElementById('loadMoreBtn');

// Utility: render posts from filteredData from currentIndex up to currentIndex + POSTS_PER_LOAD
function renderPosts(reset = false) {
  if (reset) {
    postsContainer.innerHTML = '';
    currentIndex = 0;
  }
  const slice = filteredData.slice(currentIndex, currentIndex + POSTS_PER_LOAD);
  slice.forEach(post => {
    const postElem = document.createElement('div');
    postElem.classList.add('post');
    postElem.innerHTML = `
      <img src="${post.feature_image}" alt="${post.title}" />
      <div class="post-content">
        <div class="post-title">${post.title}</div>
        <div class="post-description">${post.description}</div>
        <div class="post-meta">
          <span>By ${post.author}</span> |
          <span>${new Date(post.date_post).toLocaleDateString()}</span> |
          <span>Tags: ${post.tags.join(', ')}</span>
        </div>
      </div>
    `;
    postsContainer.appendChild(postElem);
  });
  currentIndex += POSTS_PER_LOAD;

  // Show or hide Load More button
  loadMoreBtn.style.display = currentIndex < filteredData.length ? 'block' : 'none';
}

// View toggle handlers
listViewBtn.addEventListener('click', () => {
  postsContainer.classList.remove('grid-view');
  postsContainer.classList.add('list-view');
});

gridViewBtn.addEventListener('click', () => {
  postsContainer.classList.remove('list-view');
  postsContainer.classList.add('grid-view');
});

// Advance checkbox toggle
advanceCheck.addEventListener('change', () => {
  if (advanceCheck.checked) {
    advanceFilters.classList.remove('hidden');
  } else {
    advanceFilters.classList.add('hidden');
    // Reset advance filters when hidden
    fromDateInput.value = '';
    toDateInput.value = '';
    filterTag.checked = false;
    filterTitle.checked = false;
    filterCategory.checked = false;
    enableAllCheckboxes();
  }
  applyFilters();
});

// Disable other checkboxes depending on which is checked
function handleCheckboxExclusivity() {
  if (filterTag.checked) {
    filterTitle.disabled = true;
    filterCategory.disabled = true;
  } else if (filterTitle.checked) {
    filterTag.disabled = true;
    filterCategory.disabled = true;
  } else if (filterCategory.checked) {
    filterTag.disabled = true;
    filterTitle.disabled = true;
  } else {
    enableAllCheckboxes();
  }
}
function enableAllCheckboxes() {
  filterTag.disabled = false;
  filterTitle.disabled = false;
  filterCategory.disabled = false;
}
filterTag.addEventListener('change', () => {
  handleCheckboxExclusivity();
  applyFilters();
});
filterTitle.addEventListener('change', () => {
  handleCheckboxExclusivity();
  applyFilters();
});
filterCategory.addEventListener('change', () => {
  handleCheckboxExclusivity();
  applyFilters();
});
fromDateInput.addEventListener('change', applyFilters);
toDateInput.addEventListener('change', applyFilters);

// Search input listener
searchInput.addEventListener('input', applyFilters);

// Load More button
loadMoreBtn.addEventListener('click', () => {
  renderPosts(false);
});

// Filter function logic
function applyFilters() {
  const searchText = searchInput.value.trim().toLowerCase();
  const fromDateVal = fromDateInput.value ? new Date(fromDateInput.value) : null;
  const toDateVal = toDateInput.value ? new Date(toDateInput.value) : null;

  filteredData = data.filter(post => {
    // Date filter if advance is checked
    if (advanceCheck.checked) {
      const postDate = new Date(post.date_post);
      if (fromDateVal && postDate < fromDateVal) return false;
      if (toDateVal && postDate > toDateVal) return false;
    }

    // Filter by tag/title/category checkbox logic
    if (filterTag.checked) {
      // Search by tags instead of title/author
      const tagMatch = post.tags.some(tag => tag.toLowerCase().includes(searchText));
      if (!tagMatch) return false;
    } else if (filterTitle.checked) {
      // Search by title or author
      const titleMatch = post.title.toLowerCase().includes(searchText) || post.author.toLowerCase().includes(searchText);
      if (!titleMatch) return false;
    } else if (filterCategory.checked) {
      // Category filter? The example data doesn't have a distinct category field.
      // Assuming tags include categories, let's match tags as category here.
      const categoryMatch = post.tags.some(tag => tag.toLowerCase().includes(searchText));
      if (!categoryMatch) return false;
    } else {
      // Default search by title or author if no checkbox selected
      const titleMatch = post.title.toLowerCase().includes(searchText) || post.author.toLowerCase().includes(searchText);
      if (!titleMatch) return false;
    }

    return true;
  });

  renderPosts(true);
}

// Initialize page
applyFilters();

```
