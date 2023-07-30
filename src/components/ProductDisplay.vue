<template>
  <div :class="productContainerClass" class="app-container">
    <div class="overlay">
      <img :src="backgroundOverlayImage" alt="backgroundoverlay" />
    </div>
    <div v-if="isLoading" class="loader-container">
      <!-- Tampilkan loader saat menunggu respon dari API -->
      <div class="spinner"></div>
    </div>
    <div v-else>
      <!-- Women's Clothing Section -->
      <div v-if="currentSection === 'women\'s clothing'">
        <div class="product-group">
          <!-- Foto product -->
          <div class="product-photo">
            <img :src="currentProduct.image" alt="Product Photo" />
          </div>

          <!-- Deskripsi product -->
          <div class="product-details">
            <h2 class="product-title">{{ currentProduct.title }}</h2>
            <div class="category-rating">
              <span class="category-name">{{ currentProduct.category }}</span>
              <div class="rating-form">
                <!-- Skala angka 1-5 pada rating -->
                <span v-for="i in 5" :key="i" :class="{ 'bg-navy': i <= currentProduct.rating.rate }" class="rating-circle"></span>
              </div>
            </div>
            <p class="product-description">{{ currentProduct.description }}</p>
            <p class="product-price">{{ currentProduct.price }} USD</p>
            <div class="buttons">
              <button :class="buyButtonClass" @click="buyNow">Buy Now</button>
              <button :class="nextButtonClass" @click="getNextProduct">Next Product</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Men's Clothing Section -->
      <div v-else-if="currentSection === 'men\'s clothing'">
        <div class="product-group">
          <!-- Foto product -->
          <div class="product-photo">
            <img :src="currentProduct.image" alt="Product Photo" />
          </div>

          <!-- Deskripsi product -->
          <div class="product-details">
            <h2 class="product-title">{{ currentProduct.title }}</h2>
            <div class="category-rating">
              <span class="category-name">{{ currentProduct.category }}</span>
              <div class="rating-form">
                <!-- Skala angka 1-5 pada rating -->
                <span v-for="i in 5" :key="i" :class="{ 'bg-navy': i <= currentProduct.rating.rate }" class="rating-circle"></span>
              </div>
            </div>
            <p class="product-description">{{ currentProduct.description }}</p>
            <p class="product-price">{{ currentProduct.price }} USD</p>
            <div class="buttons">
              <button :class="buyButtonClass" @click="buyNow">Buy Now</button>
               <button :class="nextButtonClass" @click="getNextProduct">Next Product</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Unavailable Product Section -->
      <div v-else>
        <div class="product-group">
          <!-- Foto sad emoticon -->
          <div class="product-photo">
            <img src="@/assets/sad-face.svg" alt="Sad Emoticon" />
          </div>

          <!-- Deskripsi product -->
          <div class="product-details">
            <h2 class="product-title">Unavailable Product</h2>
            <p class="product-description">We're sorry, this product is currently unavailable.</p>
            <div class="buttons">
               <button :class="nextButtonClass" @click="getNextProduct">Next Product</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import '../assets/style/page.css';
export default {
  data() {
    return {
      isLoading: true,
      currentProductId: 1,
      currentProduct: {},
      categories: ["women's clothing", "men's clothing"],
      currentSection: "women's clothing",
      sectionCounts: {
        "women's clothing": 20,
        "men's clothing": 20,
        "unavailable": 1, // Since there's only one "Unavailable Product" section
      },
    };
  },
  computed: {
    showProductSection() {
      return this.currentSection === "women's clothing" || this.currentSection === "men's clothing";
    },
    productContainerClass() {
      if (this.currentSection === "women's clothing") {
        return "bg-pink";
      } else if (this.currentSection === "men's clothing") {
        return "bg-blue";
      } else {
        return "bg-gray";
      }
    },
    backgroundOverlayImage() {
      if (this.currentSection === "women's clothing" || this.currentSection === "men's clothing") {
        return require('../assets/bg-pattern.svg');
      } else {
        return require('../assets/sad-face.svg');
      }
    },
  },
  async created() {
    await this.getNextProduct();
  },
  methods: {
    async fetchProduct(productId) {
      try {
        const response = await fetch(`https://fakestoreapi.com/products/${productId}`);
        const data = await response.json();

        if (
          data.category === "women's clothing" ||
          data.category === "men's clothing" ||
          data.category === "unavailable"
        ) {
          this.currentProduct = data;
          this.isLoading = false;
          this.currentSection = data.category;
        } else {
          console.log("Invalid category");
          // Handle the case when the category is invalid
        }
      } catch (error) {
        console.error(error);
        this.isLoading = false;
      }
    },

    async getNextProduct() {
      this.isLoading = true;

      if (this.currentSection === "women's clothing") {
        if (this.currentProductId >= this.sectionCounts["women's clothing"]) {
          this.currentSection = "men's clothing";
          this.currentProductId = 1;
        } else {
          this.currentProductId++;
        }
      } else if (this.currentSection === "men's clothing") {
        if (this.currentProductId >= this.sectionCounts["men's clothing"]) {
          this.currentSection = "unavailable";
          this.currentProductId = 1;
        } else {
          this.currentProductId++;
        }
      } else if (this.currentSection === "unavailable") {
        this.currentSection = "women's clothing";
        this.currentProductId = 1;
      }

      await this.fetchProduct(this.currentProductId);
    },

    buyNow() {
      // Implement the buy now functionality
      console.log("Buy now button clicked");
    },
  },
};
</script>


<style src="@/assets/style/page.css" scoped></style>
