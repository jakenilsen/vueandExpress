<template>
  <div class="container">
    <h1>Latest Posts</h1>
    <div id="create-post">
      <label for="create-post">Say Something...</label>
      <input type="text" id="create-post" v-model="text" placeholder="Create a post">
      <button v-on:click="createPost">Post!</button>
    </div>
    <hr>
    <p class="error" v-if="error">{{ error }}</p>
    <div class="posts-container">
      <loading class="loader"
               :active.sync="loading"
               :can-cancel="true"
               :opacity="0.4"
               :is-full-page="false"
               :on-cancel="onCancel"></loading>
      <div class="post"
           v-for="(post, index) in posts"
           v-bind:item="post"
           v-bind:index="index"
           v-bind:key="post._id"
           v-on:dblclick="deletePost(post._id)"
           >
        {{ `${post.createdAt.getDate()}/${post.createdAt.getMonth() +1}/${post.createdAt.getFullYear()}`}}
        <p class="text">{{ post.text }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import PostService from "@/PostService";
import Loading from 'vue-loading-overlay';
// Import stylesheet
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  name: 'PostComponent',
  data() {
    return {
      posts: [],
      error: '',
      text: '',
      loading: false
    }
  },
  async created() {
    try {
      this.loading = true;
      this.posts = await PostService.getPosts();
    }
    catch (err) {
      this.error = err.message;
    }
    this.loading = false;
  },
  components: {
    Loading
  },
  methods: {
    async createPost() {
      await PostService.insertPost(this.text);
      this.loading = true;
      this.posts = await PostService.getPosts();
      this.loading = false;
    },
    async deletePost(id) {
      await PostService.deletePost(id);
      this.loading = true;
      this.posts = await PostService.getPosts();
      this.loading = false;
    },
    onCancel() {
      console.log('User cancelled the loader.')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  div.container {
    max-width: 800px;
    margin: 0 auto;
  }
  .posts-container {
    position: relative;
  }
  .loader {
    vertical-align: top;
  }

  p.error {
    border: 1px solid #ff5b5f;
    background-color: #ffc5c1;
    padding: 10px;
    margin-bottom: 15px;
  }

  div.post {
    position: relative;
    border: 1px solid #5bd658;
    background-color: #bcffb8;
    padding: 10px 10px 30px 10px;
    margin-bottom: 15px;
  }

  div.created-at {
    position: absolute;
    top: 0;
    left: 0;
    padding: 5px 15px 5px 15px;
    background-color: darkgreen;
    color: white;
    font-size: 13px;
  }

  p.text {
    font-size: 22px;
    font-weight: 700;
    margin-bottom: 0;
  }
</style>
