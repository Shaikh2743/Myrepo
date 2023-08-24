<template>
    <div class="row">
      <div class="col-md-2 col-sm-4 col-xs-12">
        <div class="image-block">
          <img class="image" src="/assets/checkout/order-conform.png" alt="Order Conformation">
        </div>
      </div>
      <div class="col-md-8 col-sm-6 col-xs-12">
        <div class="content-block">
          <h3 class="head hidden-md">Congratulations. <br> You have successfully <br> placed the order !
            <template v-show="orderId > 0">
              <br>
              <router-link class="link-url" :to="{ path: `/my-account/orders/${getOrderId}` }">
                <span @click="changeState" class="order-id"> ORDER# :<span :class="[incrementId === '-' ? 'loading' : '']">
                  {{ incrementId }}</span></span>
              </router-link>
            </template>
          </h3>
          <h3 class="head hidden-xs">Congratulations. <br> You have successfully placed the order !
            <template v-show="orderId > 0">
              <br>
              <router-link class="link-url" :to="{ path: `/my-account/orders/${getOrderId}` }">
                <span @click="changeState" class="order-id"> ORDER# :<span :class="[incrementId === '-' ? 'loading' : '']">
                  {{ incrementId }}</span></span>
              </router-link>
            </template>
          </h3>
          <p class="text">Your items are on the way and should arrive shortly.</p>
        </div>
      </div>
      <div class="col-sm-12 track-order">
        <router-link class="link-url" :to="{ path: `/my-account/orders/${getOrderId}` }">
          <span @click="changeState">Track Your Order</span>
        </router-link>
      </div>
    </div>
  </template>
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-F5Q9BLY7H4"></script>
  <script> window.dataLayer = window.dataLayer || []; function gtag() { dataLayer.push(arguments); } gtag('js', new Date()); gtag('config', 'G-F5Q9BLY7H4');
  </script>
  
  <script>
  export default {
    name: 'OrderSuccess',
    props: {
      orderId: {
        type: Number,
        required: true
      },
      incrementId: {
        type: String,
        required: true
      },
      orderDetails: {
        type: String,
        required: true
      }
    },
    computed: {
      getOrderId () {
        if (this.orderId > 0) {
          return this.orderId
        }
        return ''
      }
    },
    methods: {
      runAdScript () {
        if (typeof window !== 'undefined' && this.orderDetails !== '') {
          let order = JSON.parse(this.orderDetails)
          order['increment_id'] = this.incrementId
          this.$bus.$emit('order-placed-tracking', {
            order
          })
          const s1 = document.createElement('script')
          const s2 = document.createElement('script')
          const s0 = document.getElementsByTagName('script')[0]
          s1.setAttribute('id', 'gtag-script-1')
          s1.innerHTML = `window.dataLayer = window.dataLayer || []; \n \
          function gtag() { \n   dataLayer.push(arguments);\n} \
          \n gtag('js', new Date());\ngtag('config', 'AW-993205101');\n \
          gtag('event', 'conversion', { 'send_to': 'AW-993205101/yXrLCLG_unUQ7bbM2QM', 'value': '${order.revenue}', 'currency': '${order.currency}', 'transaction_id': '${this.incrementId}' }); `
          s2.async = true
          s2.setAttribute('id', 'gtag-script-2')
          s2.src = 'https://www.googletagmanager.com/gtag/js?id=AW-993205101'
          s0.parentNode.appendChild(s2)
          s0.parentNode.appendChild(s1)
        } else {
          setTimeout(() => this.runAdScript(), 2000)
        }
        localStorage.removeItem('codilar-payment/analytics-state')
      },
      changeState () {
        if (typeof history === 'object') {
          let hs = history.state
          history.replaceState(hs, '', '/my-account/orders')
        }
      }
    },
    mounted () {
      let analyticsFlag = localStorage.getItem('codilar-payment/analytics-state')
      if (analyticsFlag) {
        this.runAdScript()
      }
    },
    beforeDestroy () {
      localStorage.removeItem('codilar-payment/analytics-state')
      document.getElementById('gtag-script-1').remove()
      document.getElementById('gtag-script-2').remove()
    }
  }
  </script>
  
  <style scoped>
  .order-id .loading {
    font-size: 30px;
    visibility: hidden;
  }
  
  .order-id .loading:after {
    overflow: hidden;
    visibility: visible;
    display: inline-block;
    vertical-align: bottom;
    -webkit-animation: ellipsis steps(4, end) 900ms infinite;
    animation: ellipsis steps(4, end) 900ms infinite;
    content: "\2026";
    /* ascii code for the ellipsis character */
    width: 0px;
  }
  
  @keyframes ellipsis {
    to {
      width: 1.25em;
    }
  }
  
  @-webkit-keyframes ellipsis {
    to {
      width: 1.25em;
    }
  }
  </style>
  