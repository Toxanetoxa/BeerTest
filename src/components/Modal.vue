<template>
  <div class="mymodal">
    <form>
      <div class="form-group">
        <label for="name">Name</label>
        <input
          type="text"
          class="form-control"
          id="name"
          v-model="modalTitle"
        />
      </div>
      <div class="form-group">
        <label for="exampleFormControlTextarea1">Description</label>
        <textarea
          class="form-control"
          id="exampleFormControlTextarea1"
          rows="3"
          v-model="modalDesc"
        ></textarea>
      </div>
      <div class="d-flex justify-content-end m-1">
        <button @click="savePost" type="button" class="btn btn-primary m-1">
          Save
        </button>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "Modal",
  data() {
    return {
      modalTitle: "",
      modalDesc: "",
    };
  },
  props: {
    modalVisible: Boolean,
    viewPosts: Array,
    id: Number,
  },
  methods: {
    savePost() {
      let data = {
        name: this.modalTitle,
        description: this.modalDesc,
        id: this.id,
      };
      this.$emit("savePost", data);
      this.$emit("toggle");
    },
  },
  mounted() {
    let post = this.viewPosts.find((e) => e.id === this.id);
    this.modalTitle = post.name;
    this.modalDesc = post.description;
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
