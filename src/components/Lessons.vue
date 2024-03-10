<template>
    <div>
      <div class="lessons-container" v-if="filteredLessons.length > 0">
      <div
  
        v-for="(lesson, index) in filteredLessons"
        :key="index"
        class="lesson-item"
      >
        <div class="lesson-image-container">
          <img
            :src="`https://webcoursework2.eu-north-1.elasticbeanstalk.com/${lesson.image}`"
            :alt="lesson.subject"
            class="lesson-image"
          />
        </div>
        <div class="lesson-details">
          <p><strong>Subject:</strong> {{ lesson.subject }}</p>
          <p><strong>Location:</strong> {{ lesson.location }}</p>
          <p><strong>Price:</strong> £{{ lesson.price }}</p>
          <p><strong>Spaces:</strong> {{ lesson.spaces }}</p>
        </div>
  
        <div class="lesson-actions">
          <button
            :disabled="lesson.spaces <= 0"
            v-on:click.prevent="addToCart(lesson._id)"
          >
            Add to cart
          </button>
        </div>
      </div>
      
    </div> 
  
  
   <div class="loading-container" v-if="loading">
      <iframe
        class="loading-frame"
        src="https://embed.lottiefiles.com/animation/99297"
        title="loading"
      ></iframe>
    </div>
  
    <div class="error-container" v-else-if="error">
      <iframe
        class="error-frame"
        src="https://embed.lottiefiles.com/animation/95614"
        title="error"
      ></iframe>
    </div>
  
    
  
   
    </div>
    
  </template>
  
  
  
  <script>
  export default {
      name:'Lessons',
      props:["filteredLessons"],
      methods:{
          // Add to cart feature emits function from app.vue
          addToCart(lesson){
              this.$emit("add-item-to-cart",lesson);
          }
      },
      
  
  }
  
  </script>
  
  
  