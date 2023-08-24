<template>
    <div class="slider">
      <div class="slider-container">
        <div class="slides" :style="`transform: translateX(-${slideIndex * 100}%)`">
          <div class="slide" v-for="slide in slides" :key="slide">{{ slide }}</div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        slideIndex: 0,
        slides: [
          "Slide 1: Lorem ipsum dolor sit amet.",
          "Slide 2: Consectetur adipiscing elit.",
          "Slide 3: Sed do eiusmod tempor incididunt."
        ],
        slideInterval: null
      };
    },
    mounted() {
      this.startSlideShow();
    },
    beforeDestroy() {
      clearInterval(this.slideInterval);
    },
    methods: {
      startSlideShow() {
        this.slideInterval = setInterval(() => {
          this.slideToNext();
        }, 3000); // Change slide every 3 seconds
      },
      slideToNext() {
        this.slideIndex = (this.slideIndex + 1) % this.slides.length;
      }
    }
  };
  </script>
  
  <style scoped>
  .slider {
    width: 100%;
    overflow: hidden;
  }
  
  .slider-container {
    display: flex;
    transition: transform 0.5s ease-in-out;
  }
  
  .slides {
    display: flex;
    width: 300%; /* Adjust this value based on the number of slides */
  }
  
  .slide {
    flex: 1 0 100%;
    padding: 20px;
    background-color: #ebebeb;
    text-align: center;
  }
  </style>
  