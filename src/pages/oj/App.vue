<template>
  <div>
    <NavBar v-bind:isSideMenuCollapse="isSideMenuCollapse"></NavBar>
    <LeftMenu @isCollapsed="handleExpandLeftBar"></LeftMenu>
    <div class="content-app" :class="{'with-side-menu-collapse': isSideMenuCollapse}">
      <transition name="fadeInUp" mode="out-in">
        <router-view></router-view>
      </transition>
      <div class="footer">
        <p v-html="website.website_footer"></p>
        <p>Powered by <a href="https://github.com/QingdaoU/OnlineJudge">OnlineJudge</a>
          <span v-if="version">&nbsp; Version: {{ version }}</span>
        </p>
      </div>
    </div>
    <BackTop></BackTop>
  </div>
</template>

<script>
  import { mapActions, mapState } from 'vuex'
  import NavBar from '@oj/components/NavBar.vue'
  import LeftMenu from '@oj/components/verticalMenu/leftMenu.vue'

  export default {
    name: 'app',
    components: {
      NavBar,
      LeftMenu
    },
    data () {
      return {
        version: process.env.VERSION,
        isSideMenuCollapse: false
      }
    },
    created () {
      try {
        document.body.removeChild(document.getElementById('app-loader'))
      } catch (e) {
      }
    },
    mounted () {
      this.getWebsiteConfig()
    },
    methods: {
      ...mapActions(['getWebsiteConfig', 'changeDomTitle']),
      handleExpandLeftBar (event) {
        this.isSideMenuCollapse = event
      }
    },
    computed: {
      ...mapState(['website'])
    },
    watch: {
      'website' () {
        this.changeDomTitle()
      },
      '$route' () {
        this.changeDomTitle()
      }
    }
  }
</script>

<style lang="less">

  * {
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    box-sizing: border-box;
  }

  a {
    text-decoration: none;
    background-color: transparent;
    &:active, &:hover {
      outline-width: 0;
    }
  }

.content-app {
  margin-top: 80px;
  padding: 0 2% 0 calc(2% + 180px);
  transition: all .3s;


  &.with-side-menu-collapse {
    padding: 0 2% 0 calc(2% + 78px);
    transition: all .3s;
  }
}

@media screen and (max-width: 1200px) {
  .content-app {
    margin-top: 110px;
    padding: 0 2% 0 calc(2% + 78px);
  }

  .flex-container #problem-main, .info-side {
    height: calc(100vh - 230px);
  }
  
  .CodeMirror-scroll {
    height: calc(100vh - 280px) !important;
  }
}
  @media screen and (max-width: 575px) {
    .content-app {
      margin-top: 180px;
      padding: 0 2% 0 calc(2% + 78px);
    }
  }

  @media screen and (min-width: 1200px) {
    #problem-wrapper {
      #nocode-side {
        height: calc(100vh - 149px) !important;
      }
    }

    .CodeMirror-scroll {
      height: calc(100vh - 300px) !important;
    }
  }

  .footer {
    margin-top: 20px;
    margin-bottom: 10px;
    text-align: center;
    font-size: small;
  }

  .fadeInUp-enter-active {
    animation: fadeInUp .8s;
  }


</style>
