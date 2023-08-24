<script>
import { mapState } from 'vuex'
import CurrentPage from 'theme/mixins/currentPage'
import AccountIcon from 'theme/components/core/blocks/Header/AccountIcon'
import CompareIcon from 'theme/components/core/blocks/Header/CompareIcon'
import HamburgerIcon from 'theme/components/core/blocks/Header/HamburgerIcon'
import Logo from 'theme/components/core/Logo'
import MicrocartIcon from 'theme/components/core/blocks/Header/MicrocartIcon'
import ReturnIcon from 'theme/components/core/blocks/Header/ReturnIcon'
import SearchIcon from 'theme/components/core/blocks/Header/SearchIcon'
import WishlistIcon from 'theme/components/core/blocks/Header/WishlistIcon'
import DesktopMenu from 'theme/components/core/blocks/DesktopMenu/Desktopmenu'
import CurrencySwitcher from 'theme/components/core/CurrencySwitcher/CurrencySwitcher'
import MobileIcons from './MobileIcons'

export default {
  mounted () {
    this.startSlider()
  },
  name: 'Header',
  components: {
    AccountIcon,
    CompareIcon,
    HamburgerIcon,
    Logo,
    MicrocartIcon,
    ReturnIcon,
    SearchIcon,
    WishlistIcon,
    DesktopMenu,
    CurrencySwitcher,
    MobileIcons
  },
  mixins: [CurrentPage],
  data () {
    return {
      navVisible: true,
      isScrolling: false,
      scrollTop: 0,
      lastScrollTop: 0,
      navbarHeight: 102,
      sliderInterval: null, // Added for controlling the slider interval
      slideIndex: 0 // Added to keep track of the current slide index
    }
  },
  computed: {
    ...mapState({
      isOpenLogin: state => state.ui.signUp,
      currentUser: state => state.user.current
    })
  },
  beforeMount () {
    window.addEventListener('scroll', () => {
      this.isScrolling = true
    }, { passive: true })

    this.startAutoSlide() // Added to start the automatic sliding of the slider
  },
  beforeDestroy() {
    clearInterval(this.sliderInterval) // Added to clear the slider interval when the component is destroyed
  },
  methods: {
    startSlider () {
      const slider = this.$refs.slider
      const items = slider.getElementsByClassName('text-item')
      const slideWidth = items[0].offsetWidth
      let currentIndex = 0

      const animateSlider = () => {
        currentIndex = (currentIndex + 1) % items.length
        slider.style.transform = `translateX(-${currentIndex * slideWidth}px)`
        this.slideIndex = currentIndex // Update the current slide index

        setTimeout(() => {
          setTimeout(() => {
            animateSlider()
          }, 1000)
        }, 4000)
      }

      animateSlider()
    },
    startAutoSlide() {
      this.sliderInterval = setInterval(() => {
        this.slideToNext() // Call the method to slide to the next slide
      }, 4000)
    },
    slideToNext() {
      const slider = this.$refs.slider
      const items = slider.getElementsByClassName('text-item')
      const slideWidth = items[0].offsetWidth
      const currentIndex = this.slideIndex
      const nextIndex = (currentIndex + 1) % items.length

      slider.style.transform = `translateX(-${nextIndex * slideWidth}px)`
      this.slideIndex = nextIndex // Update the current slide index
    },
    gotoAccount () {
      this.$bus.$emit('modal-toggle', 'modal-signup')
    },
    whatsappLink () {
      window.location.href = 'https://api.whatsapp.com/send?phone=917829928490'
    },
    getMenuState () {
      return this.$store.getters['header-data/getMenuState']
    },
    hasScrolled () {
      this.scrollTop = window.scrollY
      if (this.scrollTop > this.lastScrollTop && this.scrollTop > this.navbarHeight) {
        this.navVisible = false
      } else {
        if (document.body.style.overflow !== 'hidden') {
          this.navVisible = true
        }
      }
      this.lastScrollTop = this.scrollTop
    }
  }
}
</script>
