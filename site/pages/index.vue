<template>
  <section class="main">
    <div class="container main-container left-main">
      <div class="left-container">
        <div class="main-content" style="padding: 0;">
          <post-twitter @created="twitterCreated" />
        </div>
        <div class="main-content">
          <topics-nav :nodes="nodes" />
          <topic-list :topics="topicsPage.results" :show-ad="true" />
          <pagination :page="topicsPage.page" url-prefix="/topics?p=" />
        </div>
      </div>
      <topic-side :score-rank="scoreRank" :links="links" />
    </div>
  </section>
</template>

<script>
import PostTwitter from '~/components/PostTwitter'
import TopicSide from '~/components/TopicSide'
import TopicsNav from '~/components/TopicsNav'
import TopicList from '~/components/TopicList'
import Pagination from '~/components/Pagination'

export default {
  components: {
    PostTwitter,
    TopicSide,
    TopicsNav,
    TopicList,
    Pagination
  },
  async asyncData({ $axios, params }) {
    try {
      const [nodes, topicsPage, scoreRank, links] = await Promise.all([
        $axios.get('/api/topic/nodes'),
        $axios.get('/api/topic/topics'),
        $axios.get('/api/user/score/rank'),
        $axios.get('/api/link/toplinks')
      ])
      return { nodes, topicsPage, scoreRank, links }
    } catch (e) {
      console.error(e)
    }
  },
  methods: {
    twitterCreated(data) {
      if (this.topicsPage) {
        if (this.topicsPage.results) {
          this.topicsPage.results.unshift(data)
        } else {
          this.topicsPage.results = [data]
        }
      }
    }
  },
  head() {
    return {
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.$siteDescription()
        },
        { hid: 'keywords', name: 'keywords', content: this.$siteKeywords() }
      ]
    }
  }
}
</script>

<style lang="scss" scoped></style>
