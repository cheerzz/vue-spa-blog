<template>
  <div>

    <div class="article-list" v-infinite-scroll="loadMore" infinite-scroll-disabled="loading"
         infinite-scroll-distance="10">
      <PostItem v-if="articles" v-for="article in articles" :article="article"></PostItem>
    </div>
    <div class="sticky-footer">
      <Spinner v-if="loading"></Spinner>
      <Spinner v-if="loading"></Spinner>
      <Spinner v-if="loading"></Spinner>
    </div>
    <div class="ui tiny message" v-show="!next_page_url">没有了更多得文章了^_^ </div>
  </div>
</template>
<script>
  import PostItem from '../Partials/PostItem.vue';
  import Spinner from '../Partials/Spinner.vue'
  import axios from 'axios'
  import config from '../../conf/config'
  export default{
    name: "",
    data(){
      return {
        articles: [],
        loading: null,
        total: null,
        per_page: null,
        current_page: null,
        last_page: null,
        next_page_url: true,
        prev_page_url: null,
        from: null,
        to: null,
      }
    },
    methods: {
      loadMore: function () {
        if (!this.next_page_url)
          return;
        this.loading = true;
        axios.get(this.next_page_url).then((res) => {
          this.total = res.data.total
          this.per_page = res.data.per_page
          this.current_page = res.data.current_page
          this.last_page = res.data.last_page
          this.next_page_url = res.data.next_page_url
          this.prev_page_url = res.data.prev_page_url
          this.from = res.data.from
          this.to = res.data.to
          this.articles = this.articles.concat(res.data.data)
          this.loading = false
        }).catch((e) => {
          this.loading = false
        })
      }
    },
    created(){
      this.loading = true;
      axios.get(`${config.server_base_url}/api/loadmore`).then((res) => {
        this.total = res.data.total
        this.per_page = res.data.per_page
        this.current_page = res.data.current_page
        this.last_page = res.data.last_page
        this.next_page_url = res.data.next_page_url
        this.prev_page_url = res.data.prev_page_url
        this.from = res.data.from
        this.to = res.data.to
        this.articles = res.data.data
        this.loading = false
      }).catch((e) => {
        console.log(e)
      })
    },
    components: {
      PostItem,
      Spinner
    }
  };
</script>
<style scoped lang="scss" rel="stylesheet/scss">
  .article-list {
    width: 70%;
    margin: 14px auto 0 auto;
    height: 100%;
  }

  .sticky-footer {
    margin-top: 14px;
    text-align: center;
  }
  .ui.tiny.message{
    width: 70%;
    margin: 14px auto 0 auto;
  }

</style>
