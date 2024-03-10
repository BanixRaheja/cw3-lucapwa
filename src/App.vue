

<template>
<div id="app">
      <Header :siteName="siteName" :cartLength="cart.length" @change="toggleCartDisplay"> </Header>
      <!-- Header Section -->
     
      <main v-if="active === 'Lessons'" class="mt-4">
          <div v-if="!isCartDisplaying" id="lessons" class="responsive-container">
          <div class="search-container">
            <input
              type="text"
              id="search"
              placeholder="Search lessons..."
              v-model="searchText"
            />
          </div>

          <div class="filter-container">
            <p class="filter-heading">Filter by:</p>
            <div class="filter-options">
              <div
                v-for="(sortOption, index) in sortOptions"
                :key="index"
                class="filter-option"
              >
                <input
                  type="radio"
                  name="filter"
                  v-model="sortBy"
                  :value="sortOption"
                  :id="sortOption"
                />
                <label :for="sortOption" class="capitalize"
                  >{{ sortOption }}</label
                >
              </div>
            </div>
          </div>

          <div class="order-container">
            <p class="order-heading">Order by:</p>
            <div class="order-options">
              <div
                v-for="(order, index) in orders"
                :key="index"
                class="order-option"
              >
                <input
                  type="radio"
                  name="order"
                  v-model="orderBy"
                  :value="order.value"
                  :id="order.value"
                />
                <label :for="order.value" class="capitalize"
                  >{{ order.text }}</label
                >
              </div>
            </div>
          </div>

         
        </div>

        </main>


        <!-- Vue Component switching with dynamic components -->
        <component
        :is="page" :filteredLessons="filteredLessons" :cart="cart"
        :checkoutForm="checkoutForm"
        @add-item-to-cart = "addToCart" @remove-item-from-cart = "removeFromCart" :isCheckoutFormValid="isCheckoutFormValid"
        @checkout-order="checkout"
        ></component>
        

        <transition
        enter-active-class="transition ease-out duration-200 transform"
        enter-from-class="-translate-y-2"
        enter-to-class="translate-y-0"
        leave-active-class="transition ease-in duration-200 transform"
        leave-from-class="translate-y-0"
        leave-to-class="-translate-y-2"
      >
        <div
          v-if="checkedOut"
          id="toast-success"
          class="toast-container"
          role="alert"
        >
          <div class="icon-container">
            <svg
              aria-hidden="true"
              class="check-icon"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                clip-rule="evenodd"
              ></path>
            </svg>
            <span class="sr-only">Check icon</span>
          </div>
          <div class="message-container">Checked out successfully.</div>
          <button
            @click.prevent="checkedOut = false"
            type="button"
            class="close-button"
            data-dismiss-target="#toast-success"
            aria-label="Close"
          >
            <span class="sr-only">Close</span>
            <svg
              aria-hidden="true"
              class="close-icon"
              fill="currentColor"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                clip-rule="evenodd"
              ></path>
            </svg>
          </button>
        </div>
      </transition>


    </div>
</template>



<script>
import Checkout from './components/Checkout.vue';
import Lessons from './components/Lessons.vue';
import Header from './components/Header.vue'

export default {
  name: 'App',
  components: {
    Checkout,
    Lessons,
    Header
  },
  data(){
    return{
      active: Lessons,
      page: "Lessons",
      loading: false,
      siteName: "After School Club - Coursework Part2",
    error: null,
    url: "https://webcoursework2.eu-north-1.elasticbeanstalk.com",
    searchText: "",
    sortBy: "subject",
    orderBy: "asc",
    sortOptions: ["subject", "location", "price", "availability"],
    orders: [
      {
        text: "Ascending",
        value: "asc",
      },
      {
        text: "Descending",
        value: "desc",
      },
    ],
    cart: [],
    isCartDisplaying: false,
    checkedOut: false,
    checkoutForm: {
      name: {
        value: "",
        error: "",
      },
      phone: {
        value: "",
        error: "",
      },
    },
    lessons: [],
    }
  },
  methods: {
    async getLessons() {
      try {
        this.loading = true;

        const url = `${this.url}/lessons/?search=${this.searchText}`;

        const response = await fetch(url);

        this.lessons = await response.json();
      } catch (error) {
        this.error = error;
      } finally {
        this.loading = false;
      }
    },
    async createNewOrder(order) {
      try {
        this.loading = true;

        const url = `${this.url}/orders`;

        const response = await fetch(url, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(order),
        });
      } catch (error) {
        this.error = error;
      } finally {
        this.loading = false;
      }
    },
    async updateLesson({ lesson_id, spaces }) {
      try {
        this.loading = true;

        const url = `${this.url}/lessons/${lesson_id}`;

        const response = await fetch(url, {
          method: "PUT",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            spaces: spaces,
          }),
        });
      } catch (error) {
        this.error = error;
      } finally {
        this.loading = false;
      }
    },
    updateLessonSpaces(type, _id) {
      switch (type) {
        case "decrease":
          this.lessons = this.lessons.map((item) => {
            if (item._id === _id && item.spaces > 0)
              return { ...item, spaces: --item.spaces };

            return item;
          });
          break;

        case "reset":
          this.lessons = this.lessons.map((item) => {
            if (item._id === _id) return { ...item, spaces: 5 };

            return item;
          });
          break;

        default:
          break;
      }
    },
    isItemInCart(_id) {
      return !!this.cart.find((item) => item._id === _id);
    },
    addToCart(_id) {
      const lesson = this.lessons.find((lesson) => lesson._id === _id);

      if (!this.isItemInCart(lesson._id)) {
        this.cart.push({
          ...lesson,
          spaces: 1,
        });
      } else {
        if (lesson.spaces > 0) {
          this.cart = this.cart.map((item) => {
            if (item._id === _id) return { ...item, spaces: ++item.spaces };
            return item;
          });
        }
      }

      this.updateLessonSpaces("decrease", lesson._id);
    },
    removeFromCart(_id) {
      // get index of cart item
      const index = this.cart.findIndex((item) => item._id === _id);

      // remove item from cart
      this.cart.splice(index, 1);

      // update lesson spaces
      this.updateLessonSpaces("reset", _id);

      // toggle cart display if no item is in cart
      if (!this.cart.length) this.toggleCartDisplay();
    },
    toggleCartDisplay() {
     
      if(this.page == "Lessons"){
  
        this.page = "Checkout";

      }
      else if(this.page == "Checkout"){
       
        this.page = "Lessons"
      }
      else{
        this.page = "Lessons";
      }
      
      
      
      
    
    },
    checkout() {
     
      this.cart.forEach(async (item) => {
        await this.createNewOrder({
          name: this.checkoutForm.name.value,
          phone: this.checkoutForm.phone.value,
          lesson_id: item._id,
          spaces: item.spaces,
        });

        await this.updateLesson({
          lesson_id: item._id,
          spaces: item.spaces,
        });
      });

      this.checkedOut = true;

      this.active = Lessons;

      this.cart = [];

      Object.keys(this.checkoutForm).every(
        (key) => (this.checkoutForm[key].value = "")
      );

      setTimeout(() => {
        this.checkedOut = false;
      }, 3000);
    },
  },



  
  computed: {
    cartLength() {
      if (this.cart.length > 0)
        return this.cart.reduce((total, item) => total + item.spaces, 0);
      return 0;
    },
    filteredLessons() {
      if (this.lessons.length <= 0) return [];

      const lessons = this.lessons;

      if (this.orderBy === "asc") {
        // ascending
        switch (this.sortBy) {
          case "subject":
            return lessons.sort((a, b) => {
              if (a.subject.toLowerCase() > b.subject.toLowerCase()) return 1;
              if (a.subject.toLowerCase() < b.subject.toLowerCase()) return -1;
              return 0;
            });
          case "location":
            return lessons.sort((a, b) => {
              if (a.location.toLowerCase() > b.location.toLowerCase()) return 1;
              if (a.location.toLowerCase() < b.location.toLowerCase())
                return -1;
              return 0;
            });
          case "price":
            return lessons.sort((a, b) => {
              if (a.price > b.price) return 1;
              if (a.price < b.price) return -1;
              return 0;
            });
          case "availability":
            return lessons.sort((a, b) => {
              if (a.spaces > b.spaces) return 1;
              if (a.spaces < b.spaces) return -1;
              return 0;
            });
        }
      } else if (this.orderBy === "desc") {
        // descending
        switch (this.sortBy) {
          case "subject":
            return lessons.sort((a, b) => {
              if (a.subject.toLowerCase() > b.subject.toLowerCase()) return -1;
              if (a.subject.toLowerCase() < b.subject.toLowerCase()) return 1;
              return 0;
            });
          case "location":
            return lessons.sort((a, b) => {
              if (a.location.toLowerCase() > b.location.toLowerCase())
                return -1;
              if (a.location.toLowerCase() < b.location.toLowerCase()) return 1;
              return 0;
            });
          case "price":
            return lessons.sort((a, b) => {
              if (a.price > b.price) return -1;
              if (a.price < b.price) return 1;
              return 0;
            });
          case "availability":
            return lessons.sort((a, b) => {
              if (a.spaces > b.spaces) return -1;
              if (a.spaces < b.spaces) return 1;
              return 0;
            });
        }
      }
    },
    isCheckoutFormValid() {
      return Object.keys(this.checkoutForm).every(
        (key) => this.checkoutForm[key].value && !this.checkoutForm[key].error
      );
    },
  }, 
  created: function(){
    if ("serviceWorker" in navigator ) {
      navigator.serviceWorker.register("service-worker.js")
    }
  },
    
  created() {
    this.getLessons();
  },

      

  watch: {
    searchText: {
      handler(val) {
        this.getLessons();
      },
    },
    "checkoutForm.name": {
      handler(val) {
        const validationRegex = /^[A-Za-z\s]*$/;

        if (!val.value) val.error = "Please enter your name";
        else if (!validationRegex.test(val.value))
          val.error = "Your name must only contain letters";
        else val.error = "";
      },
      deep: true,
    },

    "checkoutForm.phone": {
      handler(val) {
        const validationRegex = /^((\+44)|(0)) ?\d{4} ?\d{6}$/;

        if (!val.value) val.error = "Please enter your phone number";
        else if (!validationRegex.test(val.value))
          val.error = "Please enter a valid UK phone number";
        else val.error = "";
      },
      deep: true,
    },
  },
 
 

  

  

}



</script>



<style>
body {
  font-family: 'Roboto', sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  background-color: #f1c3bc93;
}

/* Header styles */
.navbar {
  background-color: #333;
  border-bottom: 1px solid #ddd;
  padding: 10px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.navbar-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  display: flex;
  align-items: center;
  text-decoration: none;
  color: #fff;
}

.logo-image {
  margin-right: 10px;
}

.logo-text {
  font-size: 1.5rem;
  font-weight: bold;
}
.cart-button {
  background-color: transparent;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  color: #fff;
  position: fixed;
  top: 1rem;
  right: 1rem;
  z-index: 999; /* Ensure it's on top of other elements if necessary */
}

.cart-count {
  background-color: #ff9a8a93;
  color: #fff;
  border-radius: 50%;
  padding: 4px;
  margin-left: 4px;
}

/* Main content styles */
/* Search Container */
.search-container {
  margin-top: 1rem;
  display: flex;
  justify-content: center; /* Center the search box horizontally */
}

#search {
  width: 60%; /* Adjust the width as needed */
  max-width: 300px; /* Set a maximum width if necessary */
  padding: 0.5rem;
  border: 2px solid #ddd;
  border-radius: 0.25rem;
  outline: none;
  transition: border-color 0.2s ease-in-out;
}

#search:focus {
  border-color: #F73212;
}

/* Filter and Order Container */
.filter-container,
.order-container {
  margin-top: 1rem;
  display: flex;
  flex-direction: column; /* Arrange options in a column */
  align-items: center; /* Center the filter and order sections horizontally */
}

.filter-heading,
.order-heading {
  font-size: 1rem;
  font-weight: bold;
  margin-bottom: 0.5rem; /* Add some space between heading and options */
}

.filter-options,
.order-options {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  justify-content: center; /* Center the options horizontally */
}

.filter-option,
.order-option {
  display: flex;
  align-items: center;
}

/* Example: Make the filter and order options smaller */
.filter-options label,
.order-options label {
  font-size: 0.8rem;
  margin-right: 0.2rem;
}

.filter-options select,
.order-options select {
  font-size: 0.8rem;
}

/* Updated CSS for Filter and Order Container */
.filter-container,
.order-container {
  margin-top: 1rem;
  display: flex;
  justify-content: space-between;
}

.filter-heading,
.order-heading {
  font-size: 1rem;
  font-weight: bold;
  margin-right: 1rem;
}

/* Lessons Container */
.lessons-container {
  margin-top: 1rem;
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center; /* Center the lessons horizontally */
}

.lesson-item {
  background-color: white;
  border: 2px solid black;
  box-shadow: 0 2px 8px rgb(64, 47, 255);
  border-radius: 5px;
  padding: 20px;
  margin: 10px 0;
  text-align: center;
  width: calc(25% - 20px); /* Adjust the width based on your design */
  flex: 1 0 calc(25% - 1rem); /* Adjust the flex property */
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: transform 0.3s ease-in-out;
}

.lesson-item:hover {
  transform: scale(1.05);
}

.lesson-details {
  flex: 1;
}

.lesson-image {
  width: 100%; /* Make the image fill the container */
  height: auto;
  object-fit: cover;
  border-radius: 0.5rem;
  transition: transform 0.3s ease-in-out; 
}

/* Add a hover effect to scale the image slightly on hover */
.lesson-item:hover .lesson-image {
  transform: scale(1.05);
}

.lesson-info {
  text-align: center;
}

.lesson-info p {
  margin: 0.5rem 0;
}

.lesson-actions {
  margin-top: 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

.lesson-actions button {
  background-color: #ff9a8a93; /* Pink button color */
  color: #fff; /* White text color */
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  cursor: pointer;
  transition: background-color 0.3s, color 0.3s; /* Add a smooth transition effect */
}

.lesson-actions button:hover {
  background-color: rgb(64, 47, 255); /* Blue hover color */
  color: #fff; /* White text color on hover */
}

/* Loading and Error Container */
.loading-container,
.error-container {
  margin-top: 1rem;
}

.loading-frame,
.error-frame {
  width: 50%;
}
/* Lessons Container */
.lessons-container,
.empty-lessons-container {
  margin-top: 1rem;
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}

.lesson-item,
.empty-lessons-container {
  width: 100%;
}

.lesson-details {
  flex: 1;
}

.lesson-image {
  width: 10rem;
  height: 10rem;
  object-fit: cover;
  border-radius: 0.5rem;
}

.lesson-actions {
  margin-top: 0.5rem;
}

/* Empty Cart Container */
.empty-cart-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 2rem;
}

.empty-cart-icon {
  color: #A0AEC0;
}

.empty-cart-message {
  margin-top: 0.5rem;
}

/* Checkout Page Styling */
#cart {
  margin-top: 1rem;
}

.cart-heading {
  font-size: 1.5rem;
  text-align: center;
  font-weight: bold;
  margin-left: 20px;
}

/* .cart-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
} */

.cart-grid {
  margin-top: 1rem;
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center; /* Center the lessons horizontally */
}

.cart-item {
  border: 0.125rem solid #ddd;
  background-color: white;
  border-color: #333;
  border-radius: 0.3125rem;
  width: 25rem;
  padding: 1.50rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  text-align: center;
  position: relative;
  margin-left: 10x;
  margin-bottom: 20px; /* Adjust the margin-bottom to create space between cart items */
}

.cart-details {
  flex: 1;
  width: 100%;
}

.cart-image {
  width: 100%;
  height: auto; /* Adjust the height to be determined by the image */
  object-fit: cover;
  border-radius: 0.3125rem;
  margin-top: 1rem; /* Adjust the margin-top to create space between image and details */
}

.cart-actions {
  margin-top: 0.5rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Cart Summary Container */
.cart-summary-container {
  margin-top: 1rem;
}

.cart-summary {
  border: 0.125rem solid #ddd;
  border-radius: 0.3125rem;
  padding: 1.25rem;
}

.summary-heading {
  font-size: 1.25rem;
  font-weight: bold;
}

/* Checkout Form Styling */
form {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #ddd;
}

.input-container {
  display: flex;
  justify-content: space-between;
  width: 100%;
  max-width: 400px; /* Set a maximum width for the input container */
  margin-bottom: 1rem; /* Add some space between the input fields */
}

#name {
  flex: 1; /* Make the name input take up available space */
  margin-right: 0.5rem; /* Add some space between the name and phone inputs */
  border: 2px solid black;
  border-radius: 0.25rem;
  padding: 0.5rem;
  outline: none;
  transition: border-color 0.2s ease-in-out;
}

#phone {
  flex: 1; /* Make the phone input take up available space */
  margin-left: 0.5rem; /* Add some space between the name and phone inputs */
  border: 2px solid black;
  border-radius: 0.25rem;
  padding: 0.5rem;
  outline: none;
  transition: border-color 0.2s ease-in-out;
}

#name:focus,
#phone:focus {
  border-color: #F73212;
}

label {
  font-size: 1rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

input[type="text"],
input[type="tel"] {
  width: 100%;
  padding: 0.75rem;
  margin-bottom: 1rem;
  border: 0.125rem solid black;
  border-radius: 0.3125rem;
  transition: border-color 0.2s ease-in-out;
  outline: none;
}

input[type="text"]:focus,
input[type="tel"]:focus {
  border-color: #F73212;
}

button {
  background-color: #ff9a8a93;
  color: #fff;
  border: none;
  padding: 0.75rem;
  border-radius: 0.3125rem;
  cursor: pointer;
  transition: background-color 0.3s, color 0.3s;
}

button:hover {
  background-color: rgb(64, 47, 255);
}

/* Toast Container Styling */
.toast-container {
  display: flex;
  align-items: center;
  padding: 1rem;
  margin-bottom: 1rem;
  max-width: 20rem;
  color: #333;
  background-color: #fff;
  border-radius: 0.375rem;
  box-shadow: 0 0.125rem 0.25rem rgba(0, 0, 0, 0.1);
  position: fixed;
  bottom: 2rem;
  right: 4rem;
}

.icon-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 2rem;
  height: 2rem;
  margin-right: 0.75rem;
  background-color:  #ff9a8a93;
  border-radius: 0.375rem;
}

.check-icon {
  width: 1.25rem;
  height: 1.25rem;
  fill:  black;
}

.message-container {
  font-size: 0.875rem;
  font-weight: 400;
}

.close-button {
  margin-left: auto;
  margin-right: -0.375rem;
  margin-top: -0.375rem;
  background-color: #fff;
  color: #666;
  border: none;
  padding: 0.375rem;
  border-radius: 0.25rem;
  cursor: pointer;
  transition: background-color 0.3s, color 0.3s;
}

.close-button:hover {
  background-color: #F73212;
  color: #fff;
}

.close-icon {
  width: 1rem;
  height: 1rem;
  fill: #666;
}

.close-button.dark {
  background-color: #333;
  color: #fff;
}

.close-button.dark:hover {
  background-color: #F73212;
  color: #fff;
}

/* Footer Styling */
.footer {
  background-color: #333;
  color: #fff;
  padding: 1rem 0;
  text-align: center;
}

.footer-container {
  max-width: 1200px;
  margin: 0 auto;
}

.footer p {
  margin: 0;
  font-size: 0.9rem;
}
</style>