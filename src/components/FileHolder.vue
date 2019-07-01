<template>
  <div>
    <div class="file-holder" v-on:drop="handleDrop" v-on:dragover.prevent="handleOver" v-on:dragleave="handleLeave">
      <span v-if="!hasFile">{{initialHint}}</span>
      <div v-if="hasFile">File name: {{fileName}}, Size: {{fileSize}} KB</div>
      <div v-if="hovering">Place the file onto here.</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      initialHint: "Drag a file to here",
      hasFile: false,
      fileName: '',
      fileSize: 0,
      hovering: false
    };
  },
  methods: {
    handleDrop: function(event) {
      console.log('handling drop', event);
      event.preventDefault(); // .prevent alone modifier doesn't work
      let fileInfo = event.dataTransfer.files[0];
      this.hasFile = true;
      this.fileName = fileInfo.name;
      this.fileSize = fileInfo.size;
      this.hovering = false;
    },
    handleOver: function() {
      this.hovering = true;
    },
    handleLeave: function() {
      this.hovering = false;
    }
  }
};
</script>

<style scoped>
.file-holder {
  border-style: solid;
  border-width: medium;
  border-color: royalblue;
  height: 200px;
}
</style>


