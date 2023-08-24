<template>
    <div class="auth-popup">
      <div class="login-header">
        <div class="image-block">
          <p class="text visible-xs"> {{ $t('Sign Up') }}</p>
          <i
            slot="close"
            class="modal-close material-icons cl-bg-tertiary"
            @click="switchElem"
          >
            keyboard_backspace
          </i>
          <img src="/assets/account/sign-up.jpg" alt="">
        </div>
      </div>
  
      <div class="modal-content pt30 cl-secondary">
        <p class="text hidden-xs header-popup-head m0 mb30"> {{ $t('Sign Up') }}</p>
        <form @submit.prevent="register" novalidate>
          <base-input
            class="mb30"
            type="email"
            name="email"
            autocomplete="email"
            v-model="email"
            @blur="$v.email.$touch()"
            :focus="checkDevice()"
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
          <div class="relative mb30">
            <base-input
              class="col-xs-12"
              type="text"
              name="fist-name"
              autocomplete="given-name"
              v-model="firstName"
              @blur="$v.firstName.$touch()"
              :placeholder="$t('First name *')"
              :validations="[
                {
                  condition: !$v.firstName.required && $v.firstName.$error,
                  text: $t('Field is required.')
                },
                {
                  condition: !$v.firstName.minLength,
                  text: $t('Name must have at least 2 letters.')
                }
              ]"
            />
          </div>
          <div class="relative mb30">
            <base-input
              class="col-xs-12"
              type="text"
              name="last-name"
              autocomplete="last-name"
              v-model="lastName"
              @blur="$v.lastName.$touch()"
              :placeholder="$t('Last name *')"
              :validations="[{
                condition: !$v.lastName.required && $v.lastName.$error,
                text: $t('Field is required.')
              }]"
            />
          </div>
          <base-input
            class="mb30"
            type="password"
            name="password"
            ref="password"
            autocomplete="new-password"
            v-model="password"
            @blur="$v.password.$touch()"
            :placeholder="$t('Password *')"
            :validations="[
              {
                condition: !$v.password.required && $v.password.$error,
                text: $t('Field is required.')
              },
              {
                condition: !$v.password.minLength && $v.password.$error,
                text: $t('Password must have at least 8 letters.')
              }
            ]"
          />
          <base-input
            class="mb30"
            type="password"
            name="password-confirm"
            autocomplete="new-password"
            v-model="rPassword"
            @blur="$v.rPassword.$touch()"
            :placeholder="$t('Confirm password *')"
            :validations="[
              {
                condition: !$v.rPassword.required && $v.rPassword.$error,
                text: $t('Field is required.')
              },
              {
                condition: !$v.rPassword.sameAsPassword && $v.rPassword.$error,
                text: $t('Passwords must be identical.')
              }
            ]"
          />
          <div class="relative mb30">
            <base-checkbox
              class="mb30 terms"
              id="terms"
              v-model="conditions"
              @click="conditions = !conditions"
              @blur="$v.conditions.$reset()"
              @change="$v.conditions.$touch()"
              :validations="[{
                condition: !$v.conditions.required && $v.conditions.$error,
                text: $t('You must accept the terms and conditions.')
              }]"
            >
              {{ 'I accept' }} <router-link target="_blank" :to="localizedRoute('/terms-and-conditions')">terms and conditions </router-link>*
            </base-checkbox>
          </div>
          <div class="relative mb30">
            <button-full class="mb20 btn-color" type="submit" :disabled="sending">
              {{ $t('Sign Up') }}
            </button-full>
          </div>
          <div class="inline-block-full center-xs mb30">
            <span>
              {{ $t('or') }}
              <a href="#" @click.prevent="switchElem">
                {{ $t('login to your account') }}
              </a>
            </span>
          </div>
        </form>
      </div>
    </div>
  </template>
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-F5Q9BLY7H4"></script>
  <script> window.dataLayer = window.dataLayer || []; function gtag() { dataLayer.push(arguments); } gtag('js', new Date()); gtag('config', 'G-F5Q9BLY7H4');
  </script>
  <script>
  import Register from '@vue-storefront/core/compatibility/components/blocks/Auth/Register'
  import ButtonFull from 'theme/components/theme/ButtonFull.vue'
  import BaseCheckbox from 'theme/components/core/blocks/Form/BaseCheckbox.vue'
  import BaseInput from 'theme/components/core/blocks/Form/BaseInput.vue'
  import { required, email, minLength, sameAs } from 'vuelidate/lib/validators'
  
  export default {
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
      firstName: {
        minLength: minLength(2),
        required
      },
      lastName: {
        required
      },
      password: {
        minLength: minLength(8),
        required
      },
      rPassword: {
        required,
        sameAsPassword: sameAs('password')
      },
      conditions: {
        required
      }
    },
    mixins: [Register],
    components: {
      ButtonFull,
      BaseCheckbox,
      BaseInput
    },
    methods: {
      register () {
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
        this.callRegister()
      },
      onSuccess () {
        this.sending = false
        // Emit the GTM event for successful registration
        if (typeof gtag === 'function') {
          gtag('event', 'registration', {
            'event_category': 'User',
            'event_label': 'User Registration Success',
            // You can add any additional data related to the registration if needed
          })
        }
        this.$store.dispatch('notification/spawnNotification', {
          type: 'success',
          message: this.$t('You are logged in!'),
          action1: { label: this.$t('OK') }
        })
        this.$store.dispatch('codilarCustomer/ordersuccess', { })
      },
      onFailure (result) {
        this.sending = false
        this.$store.dispatch('notification/spawnNotification', {
          type: 'error',
          message: this.$t(result.result),
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
    }
  }
  </script>
  
  <style lang="scss" scoped>
    .btn-color{
      background-color: #b78642;
      text-transform: uppercase;
      font-weight: bold;
      border-radius: 4px;
    }
    .header-popup-head{
      color: black;
    }
    .terms{
      color: black;
      a{
        color: #b78642;
      }
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
  </style>
  