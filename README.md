<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Food Recipes</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      margin: 0;
      padding: 0;
      background: #fdf6f0;
    }

    header {
      background-color: #ff7f50;
      padding: 20px;
      color: white;
      text-align: center;
    }

    input[type="text"], select {
      margin: 10px;
      padding: 10px;
      border: none;
      border-radius: 5px;
    }

    .auth, .filters {
      margin-top: 10px;
    }

    main {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      padding: 20px;
    }

    .recipe-card {
      background-color: white;
      width: 250px;
      margin: 15px;
      padding: 15px;
      border-radius: 12px;
      box-shadow: 0 2px 10px rgba(0,0,0,0.1);
      text-align: center;
      cursor: pointer;
      transition: 0.3s;
    }

    .recipe-card:hover {
      transform: scale(1.05);
    }

    .recipe-card img {
      width: 100%;
      border-radius: 8px;
    }

    .recipe-card button {
      background-color: #ff7f50;
      color: white;
      padding: 5px 10px;
      margin-top: 10px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }

    /* Modal */
    .modal {
      display: none;
      position: fixed;
      z-index: 1;
      padding-top: 60px;
      background-color: rgba(0,0,0,0.7);
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
    }

    .modal-content {
      background-color: #fff;
      margin: auto;
      padding: 20px;
      border-radius: 12px;
      width: 80%;
      max-width: 600px;
      text-align: center;
    }

    .modal-content img {
      width: 100%;
      border-radius: 8px;
      margin-bottom: 10px;
    }

    .close {
      float: right;
      font-size: 24px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <header>
    <h1>🍽️ My Recipe Book</h1>
    <div class="auth">
      <input type="text" id="username" placeholder="Enter username">
      <button onclick="login()">Login</button>
    </div>
    <div class="filters">
      <input type="text" id="searchInput" placeholder="Search recipes...">
      <select id="categoryFilter">
        <option value="all">All</option>
        <option value="veg">Vegetarian</option>
        <option value="non-veg">Non-Vegetarian</option>
        <option value="dessert">Desserts</option>
      </select>
    </div>
  </header>

  <main id="recipesContainer"></main>

  <div id="modal" class="modal">
    <div class="modal-content">
      <span id="closeModal" class="close">&times;</span>
      <h2 id="modalTitle"></h2>
      <img id="modalImage">
      <p id="modalDescription"></p>
    </div>
  </div>

  <script>
    const recipes = [
      { title: "Spaghetti Bolognese", image: "images/spaghetti.jpg", description: "Classic Italian pasta.", category: "non-veg" },
      { title: "Veggie Pizza", image: "images/veggie-pizza.jpg", description: "Delicious vegetarian pizza.", category: "veg" },
      { title: "Chicken Curry", image: "images/chicken-curry.jpg", description: "Spicy chicken curry.", category: "non-veg" },
      { title: "Chocolate Cake", image: "images/cake.jpg", description: "Yummy dessert cake.", category: "dessert" }
    ];

    let currentUser = null;
    let favorites = {};

    const recipesContainer = document.getElementById("recipesContainer");
    const searchInput = document.getElementById("searchInput");
    const categoryFilter = document.getElementById("categoryFilter");
    const modal = document.getElementById("modal");
    const closeModal = document.getElementById("closeModal");
    const modalTitle = document.getElementById("modalTitle");
    const modalImage = document.getElementById("modalImage");
    const modalDescription = document.getElementById("modalDescription");

    function login() {
      const username = document.getElementById("username").value;
      if (username.trim() !== "") {
        currentUser = username;
        if (!favorites[currentUser]) favorites[currentUser] = [];
        alert(`Welcome, ${currentUser}!`);
        displayRecipes(recipes);
      }
    }

    function toggleFavorite(recipeTitle) {
      if (!currentUser) {
        alert("Please log in to favorite recipes.");
        return;
      }
      const favs = favorites[currentUser];
      const index = favs.indexOf(recipeTitle);
      if (index === -1) favs.push(recipeTitle);
      else favs.splice(index, 1);
      displayRecipes(filterRecipes());
    }

    function isFavorite(recipeTitle) {
      return currentUser && favorites[currentUser].includes(recipeTitle);
    }

    function displayRecipes(data) {
      recipesContainer.innerHTML = "";
      data.forEach(recipe => {
        const card = document.createElement("div");
        card.className = "recipe-card";
        card.innerHTML = `
          <img src="${recipe.image}" alt="${recipe.title}" />
          <h3>${recipe.title}</h3>
          <button onclick="toggleFavorite('${recipe.title}')">
            ${isFavorite(recipe.title) ? "❤️" : "🤍"} Favorite
          </button>
        `;
        card.addEventListener("click", () => {
          modalTitle.textContent = recipe.title;
          modalImage.src = recipe.image;
          modalDescription.textContent = recipe.description;
          modal.style.display = "block";
        });
        recipesContainer.appendChild(card);
      });
    }

    function filterRecipes() {
      const keyword = searchInput.value.toLowerCase();
      const category = categoryFilter.value;
      return recipes.filter(recipe => {
        const matchesSearch = recipe.title.toLowerCase().includes(keyword);
        const matchesCategory = category === "all" || recipe.category === category;
        return matchesSearch && matchesCategory;
      });
    }

    searchInput.addEventListener("input", () => {
      displayRecipes(filterRecipes());
    });

    categoryFilter.addEventListener("change", () => {
      displayRecipes(filterRecipes());
    });

    closeModal.addEventListener("click", () => {
      modal.style.display = "none";
    });

    window.onclick = function(event) {
      if (event.target == modal) modal.style.display = "none";
    };

    displayRecipes(recipes);
  </script>
</body>
</html>
