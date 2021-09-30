<template>
  <div id="app">
    <Modal
      v-if="this.modalVisible"
      @savePost="savePost"
      @toggle="toggle"
      :id="currentEdited"
      :viewPosts="viewPosts"
    />
    <div>
      <table class="table table-striped table-dark">
        <thead>
          <tr>
            <th scope="col">number</th>
            <th scope="col">name</th>
            <th scope="col">image</th>
            <th scope="col">description</th>
            <th scope="col">brewers_tips</th>
            <th scope="col">BTN</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="viewPost in viewPosts" :key="viewPost.id">
            <td scope="row">{{ viewPost.id }}</td>
            <td>{{ viewPost.name }}</td>
            <td><img class="img-thumbnail" :src="viewPost.image_url" /></td>
            <td>{{ viewPost.description }}</td>
            <td>{{ viewPost.brewers_tips }}</td>
            <td>
              <button class="btn btn-primary" @click="toggle(viewPost.id)">
                Edit
              </button>
              <button class="btn btn-danger mt-1" @click="deletePost(viewPost)">
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <button
        v-if="this.findPost || this.viewPost.length"
        class="btn btn-dark"
        @click.prevent="showNext"
      >
        {{ this.loading ? "Loading" : "Show Next" }}
      </button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Modal from "./components/Modal.vue";
export default {
  name: "App",
  components: { Modal },
  data() {
    return {
      viewPosts: [],
      count: 1,
      findPost: true,
      loading: false,
      modalVisible: false,
      currentEdited: null,
    };
  },
  methods: {
    async fetchPost() {
      this.loading = true;
      this.findPost = true;
      try {
        const res = await axios.get(
          "https://api.punkapi.com/v2/beers?page=" + this.count + "&limit=25"
        );
        if (res.data.length == 0) {
          this.findPost = false;
          console.log(this.findPost);
        }
        this.viewPosts = [...this.viewPosts, ...res.data];
      } catch (e) {
        console.log(e);
      }
      this.loading = false;
    },
    showNext() {
      this.count++;
    },
    deletePost(viewPost) {
      this.viewPosts = this.viewPosts.filter((el) => el.id !== viewPost.id);
    },
    toggle(id) {
      this.modalVisible = !this.modalVisible;
      if (this.modalVisible) {
        this.currentEdited = id;
      }
    },
    savePost(data) {
      let post = this.viewPosts.findIndex((e) => e.id === data.id);
      this.viewPosts[post].name = data.name;
      this.viewPosts[post].description = data.description;
    },
  },
  watch: {
    async count() {
      try {
        this.fetchPost();
      } catch (e) {
        console.log(e);
      }
    },
  },
  mounted() {
    this.fetchPost();
  },
};
</script>

<style></style>
