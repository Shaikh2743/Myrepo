<template>
    <div class="auth-popup">
      <div class="login-header">
        <div class="image-block">
          <p class="text visible-xs"> {{ $t('Sign In') }}</p>
          <i
            slot="close"
            class="modal-close material-icons close-right cl-bg-tertiary"
            @click="close"
          >
            close
          </i>
          <img src="/assets/account/sign-in.jpg" alt="">
        </div>
      </div>
      <div class="modal-content ">
        <p class="text hidden-xs header-popup-head"> {{ $t('Sign In') }}</p>
        <form @submit.prevent="login" novalidate>
          <base-input
            class="mb20"
            type="email"
            name="email"
            :focus="checkDevice()"
            v-model="email"
            @blur="$v.email.$touch()"
            :placeholder="$t('E-mail address *')"
            :validations="[
              {
                condition: !$v.email.required && $v.email.$error,
                text: $t('Field is required.')
              },
              {
                condition: !$v.email.email && $v.email.$error,
                text: $t('Please provide valid e-mail address.')
              }
            ]"
          />
          <base-input
            class="mb20"
            type="password"
            name="password"
            v-model="password"
            @blur="$v.password.$touch()"
            :placeholder="$t('Password *')"
            :validations="[
              {
                condition: !$v.password.required && $v.password.$error,
                text: $t('Field is required.')
            }]"
          />
          <div class="row">
            <div class="col-xs-12 col-sm-12 mb20 flex end-xs middle-xs">
              <a href="#" class="ftp" @click.prevent="remindPassword">
                {{ $t('Forgot Password?') }}
              </a>
            </div>
          </div>
          <button-full class="btn-color mb10" type="submit" data-testid="loginSubmit" :disabled="sending">
            {{ $t('Sign in') }}
          </button-full>
          <div class="center-xs dont mt20">
            {{ $t('Don\'t have an account? ') }}
            <a href="#" @click.prevent="switchElem" data-testid="registerLink">
              {{ $t('Sign up') }}
            </a>
          </div>
        </form>
        <div>
          <social-login />
        </div>
      </div>
    </div>
  </template>
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-F5Q9BLY7H4"></script>
  <script> window.dataLayer = window.dataLayer || []; function gtag() { dataLayer.push(arguments); } gtag('js', new Date()); gtag('config', 'G-F5Q9BLY7H4');
  </script>
  <script>
  import Login from '@vue-storefront/core/compatibility/components/blocks/Auth/Login'
  
  import ButtonFull from 'theme/components/theme/ButtonFull.vue'
  import BaseCheckbox from '../Form/BaseCheckbox.vue'
  import BaseInput from '../Form/BaseInput.vue'
  import { required, email } from 'vuelidate/lib/validators'
  
  import SocialLogin from './SocialLogin/Index'
  
  export default {
    mixins: [Login],
    data () {
      return {
        sending: false
      }
    },
    validations: {
      email: {
        required,
        email
      },
      password: {
        required
      }
    },
    methods: {
      login () {
        if (this.$v.$invalid) {
          this.$v.$touch()
          this.$store.dispatch('notification/spawnNotification', {
            type: 'error',
            message: this.$t('Please fix the validation errors'),
            action1: { label: this.$t('OK') }
          })
          return
        }
        this.sending = true
        this.callLogin()
      },
      remindPassword () {
        if (!(typeof navigator !== 'undefined' && navigator.onLine)) {
          this.$store.dispatch('notification/spawnNotification', {
            type: 'error',
            message: this.$t('Reset password feature does not work while offline!'),
            action1: { label: this.$t('OK') }
          })
        } else {
          this.callForgotPassword()
        }
      },
      onSuccess () {
        this.$store.dispatch('header-data/get')
        this.sending = false
        if (typeof gtag === 'function') {
          gtag('event', 'sign_in', {
            'event_category': 'User',
            'event_label': 'User Sign In Success'
            // You can add any additional data related to the sign_in if needed
          })
        }
        this.$store.dispatch('notification/spawnNotification', {
          type: 'success',
          message: this.$t('You are logged in!'),
          action1: { label: this.$t('OK') }
        })
      },
      onFailure (result) {
        this.sending = false
        this.$store.dispatch('notification/spawnNotification', {
          type: 'error',
          message: this.$t('The Account Sign-In was Incorrect, Please try again or Reset Password'),
          action1: { label: this.$t('OK') }
        })
      },
      checkDevice () {
        if (typeof window !== 'undefined') {
          let windowWidth = window.innerHeight < window.innerWidth ? window.innerHeight : window.innerWidth
          if (windowWidth >= 1024) {
            return true
          } else {
            return false
          }
        } else {
          setTimeout(() => this.checkDevice(), 2000)
        }
      }
    },
    beforeDestroy () {
      this.$store.dispatch('codilar-wishlist/getWishlist')
    },
    components: {
      ButtonFull,
      BaseCheckbox,
      BaseInput,
      SocialLogin
    }
  }
  </script>
  
  <style lang="scss" scoped>
    .modal .modal-close.close-right{
      right: 20px;
      left: auto;
      color: #000;
      top: 30px;
    }
    .login-header{
      padding-bottom: 20px;
      @media (min-width: 800px) {
        padding-bottom: 0;
      }
    }
    .btn-color{
      background-color: #b78642;
      text-transform: uppercase;
      font-weight: bold;
      border-radius: 4px;
    }
    .auth-popup .image-block{
      height: 100%;
      display: block;
      @media (min-width: 500px) {
        height: 60vh;
      }
      @media (min-width: 800px) {
        height: 618px;
      }
      @media (max-width: 812px) and (orientation: landscape) {
        height: initial;
      }
      @media only screen and (min-width: 480px) and (max-width: 800px) and (orientation: landscape){
        height: 100%;
      }
    }
    @media only screen and (min-width: 501px) and (max-width: 768px) and (orientation: portrait){
      .auth-popup .social-login-container {
         padding-bottom: 80px;
      }
    }
  </style>
  