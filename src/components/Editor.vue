<template>
  <div>
    <vue-editor
      v-model="vContent"
      class="edicol"
      useCustomImageHandler
      @image-added="handleImageAdded"
    ></vue-editor>
  </div>
</template>

<script>
import { VueEditor } from "vue2-editor";

export default {

  props: ['content'],

  components: { VueEditor },

  data() {
    return {
      vContent: this.content
    };
  },

  watch: {
    vContent(value) {
      this.$emit("input", value);
    }
  },

  methods: {
    handleImageAdded(file, Editor, cursorLocation, resetUploader) {
      var formData = new FormData();
      formData.append("file", file);

      this.$http
        .create({ withCredentials: true })
        .post(`${this.$uploads}/upload`, formData, {
          headers: {
            "Content-Type": "multipart/form-data"
          }
        })
        .then(result => {
          if (result.data.success) {
            let url = result.data.url; // Get url from response
            Editor.insertEmbed(cursorLocation, "image", url);
            resetUploader();
          } else {
            window.alert('Oops!! Something happened while uploading')
          }
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>

<style lang="scss">
@import "~vue2-editor/dist/vue2-editor.css";

/* Import the Quill styles you want */
@import "~quill/dist/quill.core.css";
@import "~quill/dist/quill.bubble.css";
@import "~quill/dist/quill.snow.css";

// [class^="ql-"] {
//   color: aqua;
// }
</style>

