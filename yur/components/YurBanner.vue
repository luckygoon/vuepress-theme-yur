<template>
  <div id="yur-banner">
    <div class="banner-page">
      <div class="img-wrapper">
        <a-skeleton
          active
          :loading="loading"
        >
          <img
            :src="banner"
            :alt="title"
          >
        </a-skeleton>
      </div>
      <div class="text-wrapper">
        <div class="title-line-wrapper">
          <div class="title-line" />
        </div>
        <h1>{{ title }}</h1>
        <p>
          <span class="subtitle">{{ subtitle }}</span>
        </p>
        <div
          v-if="bannerButtons.length"
          class="banner-btn-group"
        >
          <router-link
            v-for="(item, index) in bannerButtons"
            :key="index"
            :to="item.link"
          >
            <a-button :type="item.type">
              {{ item.text }}
            </a-button>
          </router-link>
        </div>
      </div>
    </div>
    <div class="page-1">
      <div class="page">
        <h2>
          <span>最近更新</span>
        </h2>
        <a-row v-if="updatedPosts.length">
          <a-col
            v-for="post in updatedPosts"
            :key="post.path"
            :xs="24"
            :md="8"
          >
            <router-link :to="post.path">
              <div class="banner">
                <a-skeleton
                  active
                  :loading="loading"
                >
                  <img
                    :src="$withBase(post.frontmatter.banner)"
                    :alt="post.title"
                  >
                </a-skeleton>
              </div>
              <h3>
                <span>{{ post.title }}</span>
              </h3>
            </router-link>
          </a-col>
        </a-row>
      </div>
    </div>
    <div class="page-2">
      <div class="page">
        <YurTagCloud :is-home="true" />
      </div>
    </div>
  </div>
</template>

<script>
import { init } from 'ityped'
import YurTagCloud from '@theme/components/YurTagCloud'
import { getTimeOut } from '../util'

export default {
  components: { YurTagCloud },
  props: {},
  data () {
    return {
      loading: true,
      title: '凉风有信',
      description: '责难无以成事',
      subtitle: '责难无以成事',
      banner: require('../media/images/banner.png'),
      bannerButtons: [
        { text: '阅读文章', link: '/posts/?page=1&pageSize=12', type: 'primary' },
        { text: '了解主人', link: '/about', type: 'default' },
      ],
      ityped: null,
      updatedPosts: [],
    }
  },
  computed: {},
  watch: {},
  beforeCreate () {
  },
  created () {
    this.initConfig()
    setTimeout(() => {
      this.$store.dispatch('changeSetting', {
        key: 'curtain',
        value: false,
      })
      document.getElementsByTagName('body')[0].style.overflow = 'unset'
    }, getTimeOut(this.$store.state.settings.consoleTime))
  },
  beforeMount () {
  },
  mounted () {
    if (this.ityped) {
      this.subtitle = ''
      init('span.subtitle', Object.assign({}, {
        strings: [this.description],
      }, this.ityped))
    }
    this.loading = false
  },
  beforeUpdate () {
  },
  updated () {
  },
  beforeDestroy () {
  },
  destroyed () {
  },
  methods: {
    initConfig () {
      const { title, description } = this.$site
      const { banner, bannerButtons, ityped } = this.$themeConfig
      if (title) {
        this.title = title
      }
      if (description) {
        this.description = description
      }
      if (banner) {
        this.banner = this.$withBase(banner)
      }
      if (bannerButtons) {
        this.bannerButtons = bannerButtons
      }
      if (ityped) {
        this.ityped = ityped
      }
      this.updatedPosts = Array.from(this.$posts)
      if (this.updatedPosts.length) {
        this.updatedPosts.sort((a, b) => {
          const aUpdateDate = Object.prototype.hasOwnProperty.call(a.frontmatter, 'update_date') ? new Date(a.frontmatter.update_date).getTime() : 0
          const bUpdateDate = Object.prototype.hasOwnProperty.call(b.frontmatter, 'update_date') ? new Date(b.frontmatter.update_date).getTime() : 0
          return bUpdateDate - aUpdateDate
        })
        this.updatedPosts = this.updatedPosts.slice(0, 3)
      }
    },
  },
}
</script>

<style lang="less" scoped>
</style>
