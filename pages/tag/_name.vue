<template>
  <div class="main">
    <div class="main-container">
      <div class="page-container">
        <div class="list">
          <ArticleList :articles="articles" :user="user" @add="addArticles"></ArticleList>
        </div>
      </div>
      <div class="sidebar">
        <div>
          <Sidebar :articles="hotArticles" :tags="tags" :comments="LatestComments"></Sidebar>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ArticleNav from '~/components/index/nav.vue'
import ArticleList from '~/components/article/list.vue'
import Sidebar from '~/components/common/sidebar.vue'

export default {
  components: {
    ArticleNav,
    ArticleList,
    Sidebar
  },
  validate ({ params }) {
    return !!params.name
  },
  fetch ({ store, params }) {
    let tag = store.state.tag.data.tags.find(tag => tag.name === params.name)
    return store.dispatch('getArticles', { tag: tag ? tag._id : '' })
  },
  head () {
    const tag = this.$route.params.name
    const title = tag ? tag.toLowerCase().replace(/( |^)[a-z]/g, (L) => L.toUpperCase()) : ''
    return {
      title: `${title} | 标签`
    }
  },
  created () {
    // 如果标签找不到，则返回上一个页面
    if (!this.tagID) {
      this.$router.back()
    }
  },
  computed: {
    articles () {
      return this.$store.state.article.list
    },
    hotArticles () {
      return this.$store.state.article.hot
    },
    LatestComments () {
      return this.$store.state.comment.latest
    },
    user () {
      return this.$store.state.user
    },
    tags () {
      return this.$store.state.tag
    },
    tagID () {
      let tag = this.$store.state.tag.data.tags.find(tag => tag.name === this.$route.params.name)
      return tag ? tag._id : ''
    },
    baseParams () {
      return {
        tag: this.tagID
      }
    },
    nextPageParams () {
      return Object.assign({
        page: this.articles.data.page + 1
      }, this.baseParams)
    }
  },
  methods: {
    addArticles () {
      console.log(1)
      this.$store.dispatch('getArticles', this.nextPageParams)
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
