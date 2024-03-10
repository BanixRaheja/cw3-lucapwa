<template>
    <div  id="cart" class="responsive-container">
         <h2 class="cart-heading">Shopping Cart</h2>

         <div class="cart-items-container">
           <div v-if="!!this.cart.length" class="cart-grid">
             <div v-for="(item, index) in cart" :key="index" class="cart-item">
               <div class="cart-details">
                 <p><strong>Subject:</strong> {{ item.subject }}</p>
                 <p><strong>Location:</strong> {{ item.location }}</p>
                 <p><strong>Price:</strong> £{{ item.price }}</p>
                 <p><strong>Spaces:</strong> {{ item.spaces }}</p>
               </div>
               <img
                 :src="`https://webcoursework2.eu-north-1.elasticbeanstalk.com/${item.image}`"
                 :alt="item.subject"
                 class="cart-image"
               />
               <div class="cart-actions">
                 <button v-on:click.prevent="removeFromCart(item._id)">
                   Remove
                 </button>
               </div>
             </div>
           </div>

           <div v-else class="empty-cart-container">
             <div class="empty-cart-icon">
               <svg
                 xmlns="http://www.w3.org/2000/svg"
                 fill="none"
                 viewBox="0 0 24 24"
                 stroke="currentColor"
                 class="w-16 h-16 text-gray-500"
               >
                 <path
                   stroke-linecap="round"
                   stroke-linejoin="round"
                   stroke-width="2"
                   d="M12 4v16m8-8H4"
                 ></path>
               </svg>
             </div>
             <br />
             <br />
             <p class="empty-cart-message">Your cart is empty</p>
           </div>
         </div>

         <form v-on:submit.prevent="checkout">
           <div>
             <br />
             <label for="name">Name</label>
             <input type="text" id="name" v-model="checkoutForm.name.value" />
             <p v-if="checkoutForm.name.error">
               {{ checkoutForm.name.error }}
             </p>
           </div>

           <div>
             <label for="phone">Phone</label>
             <input type="tel" id="phone" v-model="checkoutForm.phone.value" />
             <p v-if="checkoutForm.phone.error">
               {{ checkoutForm.phone.error }}
             </p>
           </div>

           <!-- Display order details in real-time -->
           <div class="order-details">
             <h2>Order Information</h2>
             <p><strong>Name:</strong> {{ checkoutForm.name.value }}</p>
             <p><strong>Phone:</strong> {{ checkoutForm.phone.value }}</p>
           </div>

           <button :disabled="!isCheckoutFormValid">
             <div v-if="loading">
               <iframe
                 class="w-1/2"
                 src="https://embed.lottiefiles.com/animation/99297"
                 title="loading"
               ></iframe>
             </div>
             <span v-else>Checkout</span>
           </button>
         </form>

    
       </div>

</template>



<script>
export default {
   name:'Checkout',
  
   props:["cart","checkoutForm", "isCheckoutFormValid"],
   methods:{
       // EVEVT Emitter
       removeFromCart(lesson){
           this.$emit("remove-item-from-cart",lesson);
          
       },
       checkout(){
           this.$emit("checkout-order");
       },

},

}

</script>
