<template>
    <div
      class="searchpanel mt60 mw-100 bg-cl-primary cl-accent"
      data-testid="searchPanel"
    >
      <div class="container">
        <div class="row">
          <div class="col-md-12 end-xs">
            <label for="search" class="visually-hidden">
              {{ $t('Search') }}
            </label>
            <div class="search-input-group">
              <input
                ref="search"
                id="search"
                class="search-panel-input"
                v-model="searchQuery"
                @input="quickSearch"
                @keyup="cSearchPage"
                :placeholder="$t('Type what you are looking for...')"
                type="text"
                autocomplete="off"
                autofocus="true"
              >
              <i
                class="material-icons search-icon"
                @click="searchButton"
              >
                search
              </i>
            </div>
          </div>
        </div>
        <template v-if="isTagalys">
          <div class="row search-filters" v-if="getCategoryFilters !== null && searchQuery !== ''">
            <div class="col-sm-12">
              <h2>Filter by Category</h2>
            </div>
            <div class="col-sm-12">
              <ul class="filters">
                <li class="filter" v-for="(category, index) in getCategoryFilters" :key="index" @click="applyFilter(category)">{{ category.name }}</li>
              </ul>
            </div>
          </div>
          <div class="row search-filters" v-else-if="selectedFilter !== ''">
            <div class="col-sm-12">
              <h2>Selected Filter</h2>
            </div>
            <div class="col-sm-12">
              <ul class="filters">
                <li class="filter selected" @click="clearFilter()">{{ selectedFilter }} <i class="material-icons">clear</i></li>
              </ul>
            </div>
          </div>
          <div class="row search-products">
            <div class="row" v-if="getSearchResults.length > 0 && searchQuery !== ''">
              <product v-for="(product, index) in getSearchResults" :product-item="product" :key="index" />
            </div>
            <div class="col-sm-12 align-center" v-else>
              <p>No Results Found</p>
            </div>
          </div>
        </template>
        <template v-else>
          <div class="quick-search-products">
            <div v-if="getQuickResult.length > 0 && searchQuery !== ''">
              <div class="col-sm-12 suggestion-link" @click="closePannel" v-for="(product, index) in getQuickResult" :key="index">
                <router-link :to="localizedRoute(`/msearch?q=${encodeURIComponent(product.name)}`)" class="links">
                  <div class="quick-link">
                    {{ product.name }}
                  </div>
                </router-link>
              </div>
            </div>
            <div class="col-sm-12 align-center" v-else>
              <p>No Results Found</p>
            </div>
          </div>
        </template>
      </div>
    </div>
  </template>
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-F5Q9BLY7H4"></script>
  <script> window.dataLayer = window.dataLayer || []; function gtag() { dataLayer.push(arguments); } gtag('js', new Date()); gtag('config', 'G-F5Q9BLY7H4');
  </script>
  <script>
  import { mapGetters, mapState, mapMutations } from 'vuex'
  import Product from './Product'
  export default {
    name: 'SearchComponent',
    components: {
      Product
    },
    data () {
      return {
        searchQuery: '',
        selectedFilter: '',
        timer: null
      }
    },
    watch: {
      isOpen (value) {
        if (value === true) {
          this.$refs.search.focus()
          this.searchQuery = ''
        }
      }
    },
    computed: {
      ...mapGetters({
        getSearchResults: 'tagalys-search/getSearchResults',
        getCategoryFilters: 'tagalys-search/getCategoryFilters',
        getQuickResult: 'codilar-listing/getQuickResult'
      }),
      ...mapState({
        isOpen: state => state['tagalys-search'].tagalysSearchModel,
        isTagalys: state => state['header-data'].is_tagalys_search
      })
    },
    methods: {
      ...mapMutations({
        closeTagalysSearchModel: 'tagalys-search/closeTagalysSearchModel'
      }),
      closePannel () {
        this.closeTagalysSearchModel()
      },
      quickSearch () {
        if (this.timer) {
          clearTimeout(this.timer)
        }
        let value = this.searchQuery
        this.timer = setTimeout(() => this.searchProducts(value), 400)
      },
      searchProducts (value) {
        let query = value
        this.selectedFilter = ''
        if (query.length >= 2) {
          if (this.isTagalys) {
            this.$store.dispatch('tagalys-search/getProductList', { query, showLoader: false })
          } else {
            this.$store.dispatch('codilar-listing/setQuickSearch', query)
          }
        }
      },
      searchLink () {
        let query = this.searchQuery
        if (query.length >= 2) {
          this.$router.push(`/search?q=${encodeURIComponent(query)}`)
        }
      },
      applyFilter (filterData) {
        let {id, name} = filterData
        let filter = {id, name}
        let query = this.searchQuery
        this.selectedFilter = name
        if (query.length >= 1) {
          this.$store.dispatch('tagalys-search/getProductList', { query, filter, showLoader: true })
        }
      },
      clearFilter () {
        let query = this.searchQuery
        this.selectedFilter = ''
        if (query.length >= 1) {
          this.$store.dispatch('tagalys-search/getProductList', { query, showLoader: true })
        }
      },
      cSearchPage (event) {
        let query = this.searchQuery
        if (event.keyCode === 13) {
           if (typeof gtag === 'function') {
            gtag(
              'event', 'search_event',
              'ecommerce':{
                'search' :[{
                  'event_category': 'Search',
                  'event_label': 'User Search',
                  'search keyword': query
                }
                ]
            // You can add any additional data related to the search_event if needed
            })
          }
          this.goToSearchPage()
        }
      },
      searchButton () {
        if (this.searchQuery !== '') {
          this.goToSearchPage()
        }
      },
      goToSearchPage () {
        let routerName = ''
        if (this.isTagalys) {
          routerName = 'tagalys-searchpage'
        } else {
          routerName = 'magento-searchpage'
        }
        this.$router.push({
          name: routerName,
          query: {
            q: this.searchQuery
          }
        })
        this.closeTagalysSearchModel()
      }
    }
  }
  </script>
  
  <style lang="scss" scoped>
    @import "~theme/css/animations/transitions";
    @import "~theme/css/variables/grid";
    @import "~theme/css/variables/typography";
    @import '~theme/css/variables/colors';
    .search-filters{
      h2{
        font-size: 22px;
        padding-left: 7px;
        margin-bottom: 5px;
      }
      .filters{
        display: inline-block;
        padding: 0;
        margin: 0;
        @media (min-width: 800px) {
          padding-left: 7px;
          margin-bottom: 10px;
        }
        .filter{
          color: $primary-color;
          list-style: none;
          display: inline-block;
          padding: 8px 15px;
          margin: 10px;
          border: 1px solid $primary-color;
          cursor: pointer;
          border-radius: 4px;
          margin-left: 0;
          font-size: 12px;
          &.selected{
            display: flex;
            align-items: center;
            color: white;
            background-color: $primary-color;
            i{
              opacity: 1;
              font-size: 15px;
              margin-left: 15px;
            }
          }
          @media (max-width: 500px) {
            padding: 11px 7px;
            margin: 6px;
            font-size: 11px;
          }
        }
      }
    }
  .quick-search-products{
    .suggestion-link{
      width: 100%;
      padding: 0;
      display: inline-block;
      border-bottom: 1px solid silver;
      cursor: pointer;
      .quick-link{
        padding: 12px 8px;
        &:hover{
          color: white;
          background-color: $primary-color;
        }
      }
    }
    @media (max-width: 767px) {
      width: 95%;
      margin: auto;
    }
  }
  .search-products{
    margin-bottom: 140px;
    .row{
      width: 102%;
    }
  }
    .searchpanel {
      height: 100vh;
      width: 928px;
      top: 0;
      right: 0;
      z-index: 3;
      overflow-y: auto;
      overflow-x: hidden;
  
      .close-icon-row {
        display: flex;
        justify-content: flex-end;
      }
  
      .container {
        padding-left: 40px;
        padding-right: 40px;
  
        @media #{$media-xs} {
          padding-left: 10px;
          padding-right: 10px;
        }
      }
  
      .row {
        margin-left: - map-get($grid-gutter-widths, lg) / 2;
        margin-right: - map-get($grid-gutter-widths, lg) / 2;
  
        @media #{$media-xs} {
          margin-right: - map-get($grid-gutter-widths, xs) / 2;
          margin-left: - map-get($grid-gutter-widths, xs) / 2;
        }
      }
  
      .col-md-12 {
        padding-left: map-get($grid-gutter-widths, lg) / 2;
        padding-right: map-get($grid-gutter-widths, lg) / 2;
  
        @media #{$media-xs} {
          padding-left: map-get($grid-gutter-widths, xs) / 2;
          padding-right: map-get($grid-gutter-widths, xs) / 2;
        }
      }
  
      .product-listing {
        padding-top: 30px;
      }
  
      .product {
        box-sizing: border-box;
        width: 100%;
        margin-bottom: 10px;
        padding-top: 20px;
        padding-bottom: 20px;
        border-bottom: 1px solid silver;
      }
  
      .close-icon {
        padding: 18px 8px;
      }
  
      .search-input-group {
        display: flex;
        border-bottom: 1px solid #bdbdbd;
        margin: auto;
        @media (max-width: 500px) {
          width: 95%;
        }
      }
  
      .search-icon {
        width: 60px;
        height: 60px;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
      }
  
      .search-panel-input {
        width: 100%;
        height: 60px;
        padding-bottom: 0;
        padding-top: 0;
        border: none;
        outline: 0;
        font-size: 18px;
  
        @media #{$media-xs} {
          font-size: 16px;
        }
      }
  
      .no-results {
        top: 80px;
        width: 100%;
      }
  
      i {
        opacity: 0.6;
      }
  
      i:hover {
        opacity: 1;
      }
  
      .close-button {
        background: #fff;
      }
  
      button {
        @media #{$media-xs} {
          width: 100%;
          margin-bottom: 15px;
        }
      }
    }
  </style>
  