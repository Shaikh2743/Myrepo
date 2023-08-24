<template>
    <div>
      <div
        class="container catalog-category-list"
        v-if="getCategoryStatus === 'OK'"
      >
        <div class="row">
          <div class="col-xs-12 breadcrumbs">
            <bread-crumbs type="category" :id="cid" />
          </div>
        </div>
        <div class="row product-container">
          <div class="p0 fs26 col-md-12">
            <h1 class="category-name">{{ getCategoryName }}</h1>
          </div>
          <div class="col-xl-3 col-lg-0 col-md-12 col-sm-12 col-xs-12 p0">
            <filter-list />
          </div>
          <div class="col-xl-9 col-lg-12 col-md-12 col-sm-12 col-xs-12">
            <category-list />
          </div>
          <div class="col-xs-12 category-description" v-show="description">
            <span v-if="readMore" v-html="description"/>
            <span v-else v-html="descriptionLess" />
            <button id="desktop-readmore-btn" class="btn btn-success desc-btn" @click="readMore =! readMore"/>
            <button id="mobile-readmore-btn" class="btn btn-success desc-btn" @click="readMore =! readMore"/>
          </div>
          <div class="col-xs-12 category-bestseller-description" v-show="bestsellerDescription">
            <span v-html="bestsellerDescription"/>
          </div>
        </div>
      </div>
      <component
        v-else-if="getCategoryStatus === 'Not found'"
        :is="'PageNotFound'"
      />
    </div>
  </template>
  <script async src="https://www.googletagmanager.com/gtag/js?id=G-F5Q9BLY7H4"></script>
  <script> window.dataLayer = window.dataLayer || []; function gtag() { dataLayer.push(arguments); } gtag('js', new Date()); gtag('config', 'G-F5Q9BLY7H4');
  </script>
  <script>
  import CategoryList from 'theme/components/Category/blocks/CategoryList'
  import FilterList from 'theme/components/Category/blocks/FilterList'
  import Category from 'src/modules/tagalys-listing/components/Category'
  import PageNotFound from '../../pages/PageNotFound'
  import BreadCrumbs from 'theme/components/core/blocks/Codilar/Breadcrumb'
  import { mapMutations } from 'vuex'
  export default {
    name: 'Catalog',
    data () {
      return {
        readMore: false,
        isMobile: false
      }
    },
    components: {
      CategoryList,
      FilterList,
      PageNotFound,
      BreadCrumbs
    },
    mixins: [Category],
    computed: {
      description () {
        let menu = this.$store.getters['header-data/mega_menu']
        let id = parseInt(this.cid)
        let description = this.getDescriptionObject(menu, id)
        if (description !== '') {
          if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
            if (this.readMore) {
              return description.description + '<button style="border: none; padding-left: 0; font-weight: 600; background: none; color: #ae9681; text-decoration: underline;" onclick="document.getElementById(\'mobile-readmore-btn\').click()">Read less</button>'
            } else {
              return description.description
            }
          } else {
            if (this.readMore) {
              return description.description + '<button style="border: none; padding-left: 0; font-weight: 600; background: none; color: #ae9681; text-decoration: underline;" onclick="document.getElementById(\'desktop-readmore-btn\').click()">Read less</button>'
            } else {
              return description.description
            }
          }
        } else {
          return false
        }
      },
      bestsellerDescription () {
        let menu = this.$store.getters['header-data/mega_menu']
        let id = parseInt(this.cid)
        let data = this.getDescriptionObject(menu, id)
        if (data !== '') {
          return data.bestseller_description
        } else {
          return false
        }
      },
      descriptionLess () {
        let menu = this.$store.getters['header-data/mega_menu']
        let data = this.getDescriptionObject(menu, id)
        let id = parseInt(this.cid)
        let description = this.getDescriptionObject(menu, id)
        if (description !== '') {
          if (typeof gtag === 'function') {
            gtag(
              'event', 'categoryview',
              'ecommerce':{
                'categoryview':[{
                  'event_category': 'category view',
                  'event_label': 'category view',
                  'category': description,
                  'id': description.id,
                  'name': description.name,
                  'url': description.url_key
                }
                ]
            // You can add any additional data related to the categoryview if needed
            })
          }
          if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
            // eslint-disable-next-line vue/no-side-effects-in-computed-properties
            this.isMobile = true
            if (description.description.length < 300) {
              return description.description.substring(0, 300)
            } else {
              return description.description.substring(0, 300) + '...' + '<button style="border: none; font-weight: 600; background: none; color: #ae9681; text-decoration: underline;" onclick="document.getElementById(\'mobile-readmore-btn\').click()">Read more</button>'
            }
          } else {
            // eslint-disable-next-line vue/no-side-effects-in-computed-properties
            this.isMobile = false
            console.log('description.length', description.description.length)
            console.log('testing category', description.description.length)
            if (description.description.length < 450) {
              return description.description.substring(0, 450)
            } else {
              return description.description.substring(0, 450) + '...' + '<button style="border: none; font-weight: 600; background: none; color: #ae9681; text-decoration: underline;" onclick="document.getElementById(\'desktop-readmore-btn\').click()">Read more</button>'
            }
          }
        } else {
          return false
        }
      }
    },
    watch: {
      $route (newValue, oldValue) {
        this.setBreadCrumbCategoryUrl(oldValue.path)
      }
    },
    methods: {
      ...mapMutations({
        setBreadCrumbCategoryUrl: 'metadata/setBreadCrumbCategoryUrl'
      }),
      getDescriptionObject (obj, value) {
        let result = ''
        Object(obj).find((e) => {
          if (e.id === value) {
            result = e
            return result
          }
          if (e.children) {
            let sub = e.children
            let child = this.getDescriptionObject(sub, value)
            if (child) {
              result = child
              return result
            }
          }
        })
        return result
      }
    },
    updated () {
      window.iFrameResize({}, '.tagalys-iframe')
    },
    beforeMount () {
      this.setBreadCrumbCategoryUrl(this.$route.path)
      this.$bus.$on('tagalys-mpage-platform-page', (data) => {
        data.details['title'] = document.title
        data.details['url'] = this.$route.path
        this.$bus.$emit('tagalys-mpage-platform', data)
      })
    },
    beforeDestroy () {
      this.$bus.$off('tagalys-mpage-platform-page')
    },
    metaInfo () {
      return {
        script: [
          {
            src: 'https://d3htxdwqp62ai4.cloudfront.net/iframeResizer.min.js'
          }
        ]
      }
    }
  }
  </script>
  
  <style lang="scss" scoped>
  @media (min-width: 800px) {
    .catalog-category-list {
      .product-container {
        width: 99%;
        margin: auto;
      }
    }
  }
  .breadcrumbs {
    p {
      margin-top: 0;
      margin-bottom: 30px;
      font-size: 15px;
      color: #565656;
    }
  }
  .category-name {
    color: #4a4a4a;
    font-size: 26px;
    margin: 5px 0;
    @media(max-width: 768px){
      margin-left: 10px;
    }
  }
  .category-description{
    margin-bottom: 10px;
    text-align: justify;
    color: #4a4a4a;
    font-size: 14px;
  }
  .category-description .desc-btn {
    background: none;
    border: none;
    color: #ae9681;
    display: none;
  }
  .category-bestseller-description{
    margin-bottom: 30px;
    text-align: justify;
    color: #4a4a4a;
    font-size: 14px;
  }
  
  </style>
  