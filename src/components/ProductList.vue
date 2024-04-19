<template>
  <div class="container">
    <!-- Search input field -->
    <input type="text" v-model="searchQuery" @input="filterProducts" class="search-input" placeholder="Search products...">
    
    <!-- Filter and sorting options -->
    <div class="filters">
      <select v-model="selectedCategory" @change="filterProducts" class="filter">
        <option value="">All Categories</option>
        <option v-for="category in categories" :key="category" :value="category">{{ category }}</option>
      </select>
      <select v-model="selectedRating" @change="filterProducts" class="filter">
        <option value="">All Ratings</option>
        <option v-for="rating in ratings" :key="rating" :value="rating">{{ rating }} Star</option>
      </select>
      <select v-model="selectedSort" @change="sortProducts" class="filter">
        <option value="">Sort by</option>
        <option value="ascending">Price: Low to High</option>
        <option value="descending">Price: High to Low</option>
      </select>
    </div>
    
    <!-- Product list -->
    <div class="product-list">
      <div v-if="filteredProducts.length === 0" class="no-products">
        No products found.
      </div>
      <div v-else v-for="product in filteredProducts" :key="product.id" class="product">
        <img :src="product.image" alt="Product Image" class="product-image">
        <div class="product-details">
          <div class="product-title">{{ product.title }}</div>
          <div class="product-price">{{ product.price }}</div>
          <div class="product-category">{{ product.category }}</div>
          <div class="product-description">{{ product.description }}</div>
          <div class="product-rating">{{ product.rating.rate }} <span class="star">&#9733;</span></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [],
      searchQuery: '', // User's search query
      filteredProducts: [], // Filtered products based on search query, rating, and category
      categories: [], // Available categories for filtering
      ratings: [1, 2, 3, 4, 5], // Available ratings for filtering
      selectedRating: '', // Selected rating filter
      selectedCategory: '', // Selected category filter
      selectedSort: '', // Selected sorting option
    };
  },
  mounted() {
    // Fetch products from the API when the component is mounted
    this.fetchProducts();
  },
  methods: {
    async fetchProducts() {
      try {
        const response = await fetch('https://fakestoreapi.com/products');
        const data = await response.json();
        this.products = data;
        this.filteredProducts = data; // Initialize filteredProducts with all products
        this.categories = [...new Set(data.map(product => product.category))]; // Extract unique categories
      } catch (error) {
        console.error('Error fetching products:', error);
      }
    },
    filterProducts() {
      // Filter products based on the search query, rating, and category
      this.filteredProducts = this.products.filter(product => {
        // Filter by search query
        const matchSearch = product.title.toLowerCase().includes(this.searchQuery.toLowerCase());
        
        // Filter by rating
        const matchRating = !this.selectedRating || Math.round(product.rating.rate) === parseInt(this.selectedRating);
        
        // Filter by category
        const matchCategory = !this.selectedCategory || product.category === this.selectedCategory;
        
        return matchSearch && matchRating && matchCategory;
      });
      
      // Apply sorting
      this.sortProducts();
    },
    sortProducts() {
      if (this.selectedSort === 'ascending') {
        // Sort products by price in ascending order
        this.filteredProducts.sort((a, b) => a.price - b.price);
      } else if (this.selectedSort === 'descending') {
        // Sort products by price in descending order
        this.filteredProducts.sort((a, b) => b.price - a.price);
      }
    },
  },
};
</script>

<style scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.search-input {
  width: 100%;
  margin-bottom: 20px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.filters {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 20px;
}

.filter {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.product-list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  grid-gap: 20px;
}

.product {
  display: flex;
  flex-direction: column;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 20px;
}

.product-image {
  width: 100%;
  height: auto;
  margin-bottom: 10px;
}

.product-details {
  flex-grow: 1;
}

.product-title {
  font-weight: bold;
  margin-bottom: 5px;
}

.product-price {
  font-size: 18px;
  margin-bottom: 5px;
}

.product-category {
  margin-bottom: 5px;
}

.product-description {
  margin-bottom: 10px;
}

.product-rating {
  display: flex;
  align-items: center;
}

.star {
  color: gold;
  margin-left: 5px;
}

.no-products {
  text-align: center;
  font-size: 18px;
  padding: 20px;
  color: #666;
}
</style>
