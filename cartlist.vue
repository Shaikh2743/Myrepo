<template>
    <div class="scroll-mobile">
      <div class="cart-items" :class="{ 'scroll-items': showActions }">
        <div class="pl8p">
          <list-item
            v-for="(item, index) in minicart.items"
            :item="item"
            :key="index"
            :show-actions="showActions"
          />
        </div>
        <div
          v-if="minicart.items.length"
          class="summary m0 cl-accent serif grand-total-summary row"
        >
          <div class="col-xs-12 px20">
            <div
              v-if="OnlineOnly && minicart.coupon_code == false"
              class="col-xs-12 coupon-wrapper margin-left-auto col-md-8"
            >
              <div class="coupon-input coupon-input-custom">
                <input
                  placeholder="Enter Coupon Code"
                  type="text"
                  id="couponinput"
                  :autofocus="true"
                  v-model.trim="couponCode"
                  @keyup.enter="setCoupon"
                >
              </div>
              <button-full
                class="p0 m0 button-outline-custom"
                color="dark"
                @click.native="setCoupon"
              >Apply</button-full
              >
            </div>
            <div class="col-xs-12 coupon-wrapper margin-left-auto col-md-8">
              <div v-show="minicart.coupon_code" class="col-xs-12 p0">
                <span
                  class="m0 py10 applied-coupon"
                >{{ minicart.coupon_code }} : Coupon Code Applied Successfully
                </span>
                <span class="remove-item"><i @click="clearCouponCode()" class="material-icons pointer cl-accent close-icon">close</i></span>
              </div>
            </div>
          </div>
          <div class="subtotals-wrapper row p0 px40">
            <div class="col-md-4 continue-shopping"/>
            <div class="sub-totals py0-cart" :class="{'fixed-item col-md-8': showActions, 'col-md-12': !showActions}">
              <div class="row m0">
                <div class="col-sm-12 summary-list">
                  <div class="total">
                    <div class="label">
                      <p>TOTAL PRICE</p>
                    </div>
                    <div class="price">
                      <p>{{ minicart.subtotal | price }}</p>
                    </div>
                  </div>
                  <div class="total coupon-code-block">
                    <div class="label" v-show="minicart.coupon_code">
                      <p class="coupon-discount-block">
                        COUPON DISCOUNT
                      </p>
                    </div>
                    <div v-show="minicart.coupon_code" class="price">
                      <p>{{ minicart.discount_amount | price }}</p>
                    </div>
                  </div>
                  <div class="total" v-if="minicart.shipping_amount >= 1">
                    <div class="label">
                      <p>SHIPPING AMOUNT</p>
                    </div>
                    <div class="price">
                      <p>{{ minicart.shipping_amount | price }}</p>
                    </div>
                  </div>
                  <div class="total">
                    <div class="label">
                      <p>GRAND TOTAL</p>
                    </div>
                    <div class="price">
                      <p class="grand-total">{{ minicart.grand_total | price }}</p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="subtotals-wrapper row btns-wrapper-container" :class="{'desk-abs-content': showActions }" v-if="minicart.items.length >= 1">
        <div class="col-md-5 continue-shopping" v-if="showActions" >
          <div class="shopping" v-if="showActions" @click="closeMicrocartExtend()" >
            <button-outline class="p0 m0 button-outline-custom hidden-xs hidden-sm button-outline-custom-cart" color="dark">{{ $t('CONTINUE SHOPPING') }}</button-outline>
          </div>
        </div>
        <div class="col-md-7">
          <div v-if="canCheckout(minicart.items)">
            <button-full
              v-if="showActions"
              class="checkout-btn checkout-btn-custom"
              @click.native="closeMicrocartExtendAndCheckout"
            >
              {{ $t("CHECKOUT") }}
            </button-full>
          </div>
          <div v-else>
            <button-full
              v-if="showActions"
              class="checkout-btn checkout-btn-custom"
              :link="{ name: 'checkout' }"
              @click.native="closeMicrocartExtend"
              :disabled="true"
            >
              {{ $t("CHECKOUT") }}
            </button-full>
          </div>
        </div>
      </div>
    </div>
  </template>
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-F5Q9BLY7H4"></script>
  <script> window.dataLayer = window.dataLayer || []; function gtag() { dataLayer.push(arguments); } gtag('js', new Date()); gtag('config', 'G-F5Q9BLY7H4');
  </script>
  <script>
  import ListItem from 'theme/components/core/blocks/Codilar/MiniCart/ListItem'
  import ButtonFull from 'theme/components/theme/ButtonFull'
  import Microcart from '@vue-storefront/core/compatibility/components/blocks/Microcart/Microcart'
  import VueOfflineMixin from 'vue-offline/mixin'
  import onEscapePress from '@vue-storefront/core/mixins/onEscapePress'
  import BaseInput from 'theme/components/core/blocks/Form/BaseInput'
  import ButtonOutline from 'theme/components/theme/ButtonOutline'
  import i18n from '@vue-storefront/i18n'
  import Minicart from 'src/modules/codilar-cart/components/Minicart'
  import { mapState } from 'vuex'
  
  export default {
    name: 'CartList',
    mixins: [Microcart, VueOfflineMixin, onEscapePress, Minicart],
    data () {
      return {
        addCouponPressed: true,
        couponCode: '',
        componentLoaded: false
      }
    },
    created() {
      console.log("Cart items:", this.minicart.items);
      if (typeof gtag === 'function') {
            gtag(
              'event', 'cart_items',
              'ecommerce':{
                'cartitems':[{
                  'event_category': 'cart items',
                  'event_label': 'Cart Items',
                  'cart_items': this.minicart.items
                }]
            // You can add any additional data related to the Cart items if needed
            })
          }
    },
    props: {
      minicart: {
        type: Object,
        required: true
      },
      showActions: {
        type: Boolean,
        required: false,
        default: false
      }
    },
    components: {
      ListItem,
      ButtonFull,
      ButtonOutline,
      BaseInput
    },
    computed: {
      ...mapState({
        currentUser: state => state.user.current
      })
    },
    methods: {
      clearCouponCode () {
        this.addCouponPressed = true
        this.$store.dispatch('codilar-cart/removeCoupon')
      },
      closeMicrocartExtendAndCheckout () {
        this.addCouponPressed = false
        this.$store.dispatch('ui/toggleMicrocart')
        this.$store.commit('ui/setSidebar', false)
        this.$bus.$emit('proceed-to-checkout', {
          step: 1,
          option: 'Personal Details'
        })
        this.$router.push('/checkout')
      },
      closeMicrocartExtend () {
        this.addCouponPressed = false
        this.$store.dispatch('ui/toggleMicrocart')
        this.$store.commit('ui/setSidebar', false)
      },
      addDiscountCoupon () {
        this.addCouponPressed = true
      },
      gotoAccount () {
        this.$store.dispatch('ui/toggleMicrocart')
        this.$store.commit('ui/setSidebar', false)
        this.$bus.$emit('modal-toggle', 'modal-signup')
      },
      clearCoupon () {
        this.removeCoupon()
        this.addCouponPressed = true
      },
      setCoupon () {
        if (this.couponCode.length === 0 || this.couponCode.length === null) {
          this.$store.dispatch('notification/spawnNotification', {
            type: 'warning',
            message: i18n.t(
              'Enter Coupon Code'
            ),
            action1: { label: i18n.t('OK') }
          })
        } else {
          console.log(this.couponCode, 'coupon code');
          if (typeof gtag === 'function') {
            gtag(
              'event', 'coupon_code',
              'ecommerce':{
                'couponcode':[{
                  'event_category': 'coupon code',
                  'event_label': 'Coupon code',
                  'coupn code': this.couponCode
                }]
            // You can add any additional data related to the coupon code if needed
            })
          }
          this.applyCoupon(this.couponCode)
            .then(() => {
              this.addCouponPressed = false
              this.couponCode = ''
              this.$store.dispatch('codilar-cart/get')
              this.$store.dispatch('notification/spawnNotification', {
                type: 'success',
                message: i18n.t('Coupon Applied'),
                action1: { label: i18n.t('OK') }
              })
            })
            .catch(() => {
              this.$store.dispatch('notification/spawnNotification', {
                type: 'warning',
                message: i18n.t(
                  "You've entered an incorrect coupon code. Please try again."
                ),
                action1: { label: i18n.t('OK') }
              })
            })
        }
      },
      // closeMicrocartExtend () {
      //   this.closeMicrocart()
      //   this.$store.commit('ui/setSidebar', false)
      //   this.addCouponPressed = false
      // },
      onEscapePress () {
        this.closeMicrocart()
      },
      canCheckout (items) {
        let canCheckout = true
        items.map(item => {
          if (item.stock_status === false) {
            canCheckout = false
          }
        })
        return canCheckout
      }
    }
  }
  </script>
  
  <style lang="scss" scoped>
  .scroll-items {
    overflow-y: auto;
    overflow-x: hidden;
    height: calc(100vh - 170px);
    @media(max-width: 767px) {
       height: auto;
     }
  }
  .subtotals-wrapper {
    width: 100%;
    @media (min-width: 768px) {
      position: relative;
    }
    .subtotals-wrapper{
      width: 100%;
      .continue-shopping{
        align-items: baseline;
        display: flex;
        flex-direction: column-reverse;
        padding-bottom: 15px;
        .shopping{
          width: 100%;
        }
        button{
          color: #4a4a4a;
          border-radius: 4px;
          max-width: 250px;
          z-index: 1;
          height: 50px;
          padding: 0 20px;
          text-align: center;
          width: 100%;
          display: block;
          font-size: 14px;
          &:hover{
            background: none;
          }
          &:focus{
            background: none;
          }
        }
        @media(max-width: 768px) {
          display: none;
        }
      }
    }
  }
  .sub-totals {
    .total {
      display: flex;
      p {
        margin: 0;
        padding: 15px 0;
      }
      .label {
        flex: 2 1;
        p {
          color: #4a4a4a;
        }
        @media (max-width: 767px) {
          flex: 2.5 1;
        }
      }
      .price {
        flex: 2 1;
        p {
          font-weight: bold;
          text-align: right;
          &.grand-total {
            font-size: 20px;
            @media (max-width: 767px) {
              font-size: 18px;
            }
          }
        }
        @media (max-width: 767px) {
          flex: 1.5 1;
        }
      }
    }
    &.fixed-item {
      width: 100%;
      @media (min-width: 800px) {
        background-color: #f4f4f4;
        padding: 15px 15px;
        box-sizing: border-box;
        .total {
          p {
            margin: 0;
            padding: 10px 0;
          }
        }
      }
    }
  }
  .coupon-wrapper {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    button {
      width: max-content;
      padding: 8px;
      min-width: 148px;
      font-size: 14px;
      height: 38px;
      text-transform: uppercase;
      border-radius: 0 4px 4px 0;
      @media (max-width: 768px) {
        min-width: 100px;
      }
    }
  }
  .grand-total-summary {
    background: #f4f4f4;
    padding-top: 20px;
    // position: absolute;
    // width: 100%;
    // bottom: 0;
    // left: 0;
    @media (max-width: 768px) {
      // position: relative !important;
      padding: 20px 0 0 0;
      margin: 0;
      .subtotals-wrapper {
        .summary-list {
          padding: 0 20px;
          @media (max-width: 768px) {
            .total:not(:last-child) {
              border-bottom: 1px solid #ededed;
            }
          }
        }
        @media (max-width: 768px) {
          padding: 0;
          margin: 0;
        }
      }
    }
  }
  .applied-coupon {
    font-size: 18px;
    padding-right: 15px;
    display: inline;
    color: #9d7f64 !important;
    @media (max-width: 768px) {
      margin-bottom: 10px;
      padding-right: 5px;
      font-size: 14px;
      width: calc(100% - 25px);
      display: block;
      float: left;
    }
  }
  .coupon-input {
    width: calc(100% - 148px);
    overflow: hidden;
    @media (max-width: 768px) {
      width: calc(100% - 100px);
    }
    #couponinput {
      width: 100%;
      padding-top: 6px;
      padding-bottom: 6px;
      height: 40px;
      border: 1px solid #eeeeee;
      background: #ffffff;
      padding-left: 10px;
      border-top-left-radius: 5px;
      border-bottom-left-radius: 5px;
      font-size: 12px;
      vertical-align: text-bottom;
      line-height: 1;
      outline: none;
      &:focus {
        outline: none;
      }
    }
  }
  .bottom-wrapper {
    position: absolute;
    bottom: 0;
    padding-bottom: 20px;
    width: 100%;
    display: flex;
    align-items: center;
    background: #f4f4f4;
    justify-content: space-evenly;
    .shopping {
      button {
        color: #4a4a4a;
        background: #f4f4f4;
        border-radius: 4px;
        min-width: 190px;
        z-index: 1;
        height: 60px;
        padding: 0 20px;
        text-align: center;
        width: 100%;
        display: block;
        bottom: 35px;
        width: 18%;
      }
    }
    .checkout-btn {
      max-width: 250px;
      border-radius: 4px;
      @media (max-width: 768px) {
        position: fixed;
        bottom: 0;
        left: 0;
        right: 0;
        width: 100%;
        border-radius: 0;
      }
    }
  }
  .margin-left-auto {
    margin-left: auto;
    margin-right: 20px;
    padding-left: 24px;
    @media (max-width: 767px) {
      margin-left: 0;
      margin-right: 0;
      padding-left: 0;
      padding-right: 0;
    }
  }
  .subtotals-wrapper {
    .py0-cart {
      padding-left: 0;
      padding-right: 0;
    }
  }
  .pl8p {
    padding-left: 8%;
    @media (max-width: 767px) {
      padding-left: 20px;
    }
  }
  @media (max-width: 767px) {
    .scroll-mobile {
      height: calc(100% - 210px);
      overflow: auto;
    }
  }
    @media (min-width: 768px){
      .coupon-wrapper{
        .applied-coupon{
          margin-bottom: 10px;
          padding-right: 5px;
          width: calc(100% - 25px);
          display: block;
          float: left;
        }
        .remove-item{
          margin-top: 10px;
          width: 20px;
          float: right;
        }
      }
      .btns-wrapper-container{
        padding-left: 7%;
        padding-right: 30px;
        padding-bottom: 15px;
      }
      .btns-wrapper-container{
        .button-outline-custom-cart{
          max-width: 250px;
          font-size: 14px;
          color: #4a4a4a;
          background: transparent;
          border-radius: 4px;
          height: 52px;
        }
      }
      .checkout-btn-custom{
        max-width: 250px;
        float: right;
        border-radius: 4px;
        padding: 18px;
        height: 52px;
        font-size: 14px;
      }
      .desk-abs-content{
        position: absolute;
        bottom: 0;
        background: #f4f4f4;
        padding-top: 20px;
        left: 0;
        right: 0;
      }
      .scroll-items{
        .min-height-list-item{
          min-height: calc(100vh - 420px);
        }
      }
    }
  @media (max-width: 767px) {
    .checkout-btn-custom{
      position: fixed;
      bottom: 0;
      left: 0;
      right: 0;
      width: 100%;
      border-radius: 0;
    }
  }
    .coupon-code-block{
      .coupon-discount-block{
        display: inline-block;
      }
      .remove-coupon{
        display: inline-block;
      }
    }
  .remove-item{
    display: inline-block;
    margin-bottom: 20px;
    @media (max-width: 767px){
      margin-bottom: 10px;
      margin-top: 6px;
    }
    i{
      color: white;
      width: 20px;
      height: 20px;
      background-color: rgb(112,112,112);
      font-size: 14px;
      text-align: center;
      align-items: center;
      display: grid;
      border-radius: 15px;
    }
  }
  @media (min-width: 767px) and (max-width: 1024px) {
    .desk-abs-content{
      left: 8px;
      padding-right: 15px;
      .checkout-btn-custom{
        position: static;
      }
    }
  }
  </style>
  <style lang="scss">
    @media (min-width: 767px) and (max-width: 1024px) {
      .right-sidebar-custom{
        .title-main{
          padding-left: 5% !important;
        }
      }
      .scroll-items{
        .min-height-list-item{
          min-height: calc(100vh - 400px) !important;
          }
      }
      .grand-total-summary{
        .margin-left-auto{
          margin-right: -8px;
          padding-left: 3px;
        }
      }
    }
  </style>
  