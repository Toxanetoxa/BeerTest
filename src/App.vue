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
        v-if="this.findPost || this.viewPost.length !== 0"
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
      return;
    },
    toggle(id) {
      this.modalVisible = !this.modalVisible;
      if (this.modalVisible) {
        this.currentEdited = id;
      }
    },
    inputName(event) {
      this.name = event.target.value;
    },
    savePost(data) {
      let post = this.viewPosts.findIndex((e) => e.id === data.id);
      this.viewPosts[post].name = data.name;
      this.viewPosts[post].description = data.description;
    },
  },
  watch: {
    async count() {
      if (this.count <= 0) {
        return;
      }
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

<style>
.mymodal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  flex-direction: column;
  width: 80%;
  background: white;
  border: black solid 1px;
  border-radius: 15px;
  padding: 10px;
  justify-content: flex-end;
}
.btn {
  justify-self: flex-end;
}
</style>
