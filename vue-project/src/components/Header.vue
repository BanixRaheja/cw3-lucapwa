<template>
     <header>
        <!-- Navigation -->
        <nav class="navbar">
          <div class="navbar-container">
            <a href="javascript:void(0)" class="logo">
              <img
                src="https://ik.imagekit.io/0vdhtgagut/icon_uad6rbD3V?updatedAt=1706410663671"
                alt="Flowbite Logo"
                class="logo-image"
              />
              <span class="logo-text"
                >{{ siteName }}</span
              >
            </a>

            <button
              class="cart-button"
              :class="[cartLength <= 0 ? 'empty' : 'notEmpty']"
              :disabled="cartLength <= 0"
              @click ="toggleCartDisplay"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
                class="cart-icon"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  d="M2.25 3h1.386c.51 0 .955.343 1.087.835l.383 1.437M7.5 14.25a3 3 0 00-3 3h15.75m-12.75-3h11.218c1.121-2.3 2.1-4.684 2.924-7.138a60.114 60.114 0 00-16.536-1.84M7.5 14.25L5.106 5.272M6 20.25a.75.75 0 11-1.5 0 .75.75 0 011.5 0zm12.75 0a.75.75 0 11-1.5 0 .75.75 0 011.5 0z"
                />
              </svg>
              <span class="cart-count">{{ cartLength }}</span>
            </button>
          </div>
        </nav>
      </header>
</template>


<script>

export default{
    name:'Header',
    props:["siteName", "cartLength"],
    methods:{
        toggleCartDisplay(){
            console.log("event has been triggered");
            this.$emit("change") ;
        }
    }

}
</script>


<style scoped>
</style>