<template>
    <div class="row">
      <div class="col-md-12 col-sm-6 col-xs-12">
        <div class="content-block">
          <h3 class="head">Oops. <br>There was an error placing your order</h3>
          <p class="text">REASON: {{ message }}</p>
          <p class="text">Please contact our customer care for further assistance</p>
        </div>
      </div>
    </div>
  </template>
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-F5Q9BLY7H4"></script>
  <script> window.dataLayer = window.dataLayer || []; function gtag() { dataLayer.push(arguments); } gtag('js', new Date()); gtag('config', 'G-F5Q9BLY7H4');
  </script>
  <script>
  export default {
    name: 'OrderFailure',
    props: {
      message: {
        type: String,
        required: true
      }
    },
    mounted () {
      // Push the order failure event to the dataLayer
      // if (window.dataLayer) {
      //   window.dataLayer.push({
      //     event: 'orderFailure', // Use your desired event name
      //     failureReason: this.message // Pass the failure reason as data
      //   })
      // },
      if (typeof gtag === 'function') {
            gtag(
              'event', 'order_cancelled',
              'ecommerce':{
                'ordercancelled':[{
                  'event_category': 'cancelled',
                  'event_label': 'cancelled ',
                }]
            // You can add any additional data related to the order_cancelled if needed
            })
          }
    }
  }
  </script>
  
  <style scoped>
  
  </style>
  