
<template>
  <div class="back">
    <h1 class="heading">Product Page</h1>
    
    <!-- Category Cards -->
    <div class="category-cards">
      <div
        v-for="category in uniqueCategories"
        :key="category"
        @click="selectCategory(category)"
        class="category-card"
        :class="{ active: selectedCategory === category }"
      >
        <h3>{{ category }}</h3>
        <div v-if="selectedCategory === category">
          <div v-for="product in filteredProductsByCategory(category)" :key="product.jewelID" class="product-card">
            <div class="description">
              <h3>{{ product.jewelName }}</h3>
              <img :src="product.jewelImage" alt="Product Image" style="width: 100px; height: auto;">
              <p>Price: R{{ product.jewelAmount }}</p>
              <button @click="openModal(product)" class="button">View Details</button>
              <button class="button">Get in touch</button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal -->
    <Modal :show-modal="showModal" :product-data="selectedProduct" @close="closeModal" />
  </div>
</template>

  <script>
  import Modal from '../components/Modal.vue'
  
  export default {
    components: {
      Modal
    },
    data() {
      return {
        selectedCategory: null,
        minPrice: null,
        maxPrice: null,
        searchQuery: "",
        showModal: false,
        selectedProduct: {}
      };
    },
    computed: {
      uniqueCategories() {
        const categories = new Set();
        if (this.$store.state.jewellery) {
          this.$store.state.jewellery.forEach(product => {
            categories.add(product.jewelCategory);
          });
        }
        return Array.from(categories);
      },
      filteredProducts() {
        let filteredProducts = this.$store.state.jewellery;
  
        // Filter by category
        if (this.selectedCategory) {
          filteredProducts = filteredProducts.filter(product => product.jewelCategory === this.selectedCategory);
        }
  
        // Filter by price range
        if (this.minPrice !== null && this.maxPrice !== null) {
          filteredProducts = filteredProducts.filter(product => product.jewelAmount >= this.minPrice && product.jewelAmount <= this.maxPrice);
        }
  
        // Search query filter
        if (this.searchQuery.trim() !== "") {
          const query = this.searchQuery.toLowerCase();
          filteredProducts = filteredProducts.filter(product =>
            product.jewelName.toLowerCase().includes(query) ||
            product.jewelDescription.toLowerCase().includes(query)
          );
        }
  
        return filteredProducts;
      },
      filteredProductsByCategory() {
        return category => {
          return this.filteredProducts.filter(product => product.jewelCategory === category);
        };
      }
    },
    methods: {
      selectCategory(category) {
        this.selectedCategory = this.selectedCategory === category ? null : category;
      },
      openModal(product) {
        this.selectedProduct = product;
        this.showModal = true;
        document.body.classList.add('modal-open');
      },
      closeModal() {
        this.showModal = false;
        document.body.classList.remove('modal-open');
      },
      async fetchData() {
        try {
          await this.$store.dispatch('fetchJewellery');
        } catch (error) {
          console.error('Error fetching jewellery:', error);
        }
      }
    },
    created() {
      this.fetchData();
    }
  }
  </script>


<style>
.heading{
  font-size: 60px;
  text-transform: uppercase;
  font-family: 'Gambetta', serif;
  letter-spacing: -3px;
  transition: 700ms ease;
  font-variation-settings: "wght" 311;
  margin-bottom: 0.8rem;
  color: PaleGoldenRod;
  outline: none;
  text-align: center;
  z-index: 100;
}
.heading:hover{
  font-variation-settings: "wght" 582; 
  letter-spacing: 1px;
}
.back{
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 100%;
  height: 1100vh;
  background: linear-gradient(-50deg, rgb(244, 238, 129), #060606, rgb(7, 7, 7), #9f9da0);
  background-size: 200% 200%; /* Increase the size to cover the entire animation */
  animation: fire 7s ease infinite;
  z-index: -1;
}

@keyframes fire {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}
.product-card {
  display: inline-block;
  width: calc(33.33% - 20px);
  margin: 33px;
  height: 285px;
  vertical-align: top;
  position: relative; /* Required for absolute positioning of pseudo-elements */
  background: aliceblue;
  z-index: 1;
}

.product-card::before,
.product-card::after {
  content: '';
  position: absolute;
  top: -5px;
  left: -5px;
  width: calc(100% + 0px);
  height: calc(100% + 0px);
  border: 4px solid #ede218; /* Neon green color */
  border-radius: 5px;
  z-index: -1;
  animation: neon-border 1.5s infinite alternate; /* Neon animation */
}

@keyframes neon-border {
  from {
    opacity: 1;
  }
  to {
    opacity: 0.2;
  }
}
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 15px 30px;
  border: 0;
  position: relative;
  overflow: hidden;
  border-radius: 10rem;
  transition: all 0.02s;
  font-weight: bold;
  color: rgb(37, 37, 37);
  z-index: 0;
  box-shadow: 0 0px 7px -5px rgba(0, 0, 0, 0.5);
}

.button:hover {
  background: rgb(193, 228, 248);
  color: rgb(33, 0, 85);
}

.button:active {
  transform: scale(0.97);
}

.hoverEffect {
  position: absolute;
  bottom: 0;
  top: 0;
  left: 0;
  right: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.hoverEffect div {
  background: rgb(222,0,75);
  background: linear-gradient(90deg, rgba(222,0,75,1) 0%, rgba(191,70,255,1) 49%, rgba(0,212,255,1) 100%);
  border-radius: 40rem;
  width: 10rem;
  height: 10rem;
  transition: 0.4s;
  filter: blur(20px);
  animation: effect infinite 3s linear;
  opacity: 0.5;
}

.button:hover .hoverEffect div {
  width: 8rem;
  height: 8rem;
}

@keyframes effect {

  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}

.modal {
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #fefefe;
  padding: 20px;
  border-radius: 5px;
  max-width: 600px; /* Adjust the maximum width as needed */
  width: 80%;
}

.close {
  position: absolute;
  top: 10px;
  right: 10px;
  font-size: 28px;
  color: #aaa;
  cursor: pointer;
}

.close:hover {
  color: black;
}

.card {
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 20px;
  background-color: white;
}

.card__title {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.card__title .icon {
  margin-right: 10px;
}

.card__title h3 {
  margin: 0;
}

.card__body {
  display: flex;
}

.half {
  flex: 1;
  padding: 0 10px;
}

.image img {
  max-width: 100%;
  height: auto;
}

.description p {
  margin-bottom: 10px;
}

.stock {
  display: block;
  margin-top: 10px;
  color: #666;
}

.fa {
  color: #666;
}


/* Show modal */
.modal-open {
  overflow: hidden;
}

.modal-open .modal {
  display: block;
}


@media only screen and (max-width: 300px) {
  .heading {
    font-size: 30px; /* Adjust heading font size for smaller screens */
  }

  .back {
    height: auto; /* Allow background height to adjust based on content */
  }

  .product-card {
    width: 100%; /* Adjust product card width to fit the full screen */
    margin: 10px 0; /* Adjust margin for better spacing */
    height: auto; /* Allow product card height to adjust based on content */
  }

  .product-card::before,
  .product-card::after {
    display: none; /* Hide neon border on smaller screens */
  }

  .button {
    width: 100%; /* Adjust button width to fit the full screen */
  }

  .modal-content {
    width: 90%; /* Adjust modal content width for better fit on smaller screens */
    margin: 10% auto; /* Adjust margin for better centering on smaller screens */
  }
}
</style>