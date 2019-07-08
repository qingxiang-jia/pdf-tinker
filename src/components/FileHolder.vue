<template>
  <div>
    <div
      class="file-holder"
      v-on:drop="handleDrop"
      v-on:dragover.prevent="handleOver"
      v-on:dragleave="handleLeave"
    >
      <div class="hint" v-if="!hasFile">{{initialHint}}</div>
      <div class="file-info" v-if="hasFile">
        <div class="file-info-row">
          <div class="file-info-cell">File name:</div>
          <div class="file-info-cell">{{fileName}}</div>
        </div>
        <div class="file-info-row">
          <div class="file-info-cell">Size:</div>
          <div class="file-info-cell">{{fileSize}} KB</div>
        </div>
      </div>
      <div class="preview">
        <canvas v-for="preview in previews" v-bind:key="preview.id" class="preview-item" :id="preview.id"></canvas>
      </div>
      <div v-if="hovering">Place the file onto here.</div>
    </div>
    <canvas id="canvas"></canvas>
  </div>
</template>

<script>
import pdfjsLib from "../../node_modules/pdfjs-dist/webpack";
const PREVIEW_HEIGHT = 180; // px

export default {
  data() {
    return {
      initialHint: "Drag a file to here",
      hasFile: false,
      fileName: "",
      fileSize: 0,
      hovering: false,
      previews: []
    };
  },
  methods: {
    handleDrop(event) {
      event.preventDefault(); // .prevent alone doesn't work
      let fileInfo = event.dataTransfer.files[0];
      this.hasFile = true;
      this.fileName = fileInfo.name;
      this.fileSize = fileInfo.size;
      this.hovering = false;
      // render the pdf
      let file = event.dataTransfer.files[0];
      let pdfPath = URL.createObjectURL(file);
      this.genPreview(pdfPath);
    },
    handleOver() {
      this.hovering = true;
    },
    handleLeave() {
      this.hovering = false;
    },
    genPreview(pdfPath) {
      let loadingTask = pdfjsLib.getDocument(pdfPath);
      loadingTask.promise.then((pdf) => {
        let numOfPages = pdf.numPages;
        for (let pageIndex = 0; pageIndex < numOfPages; pageIndex++) {
          this.previews.push({id: `preview-item-${pageIndex}`});
          pdf.getPage(pageIndex + 1).then((page) => {
            let canvas = document.getElementById(`preview-item-${pageIndex}`);
            let context = canvas.getContext("2d");
            let viewport = page.getViewport(1.0);
            viewport = page.getViewport(PREVIEW_HEIGHT / viewport.height);
            canvas.height = viewport.height;
            canvas.width = viewport.width;
            let renderContext = {
              canvasContext: context,
              viewport: viewport
            };
            page.render(renderContext);
          });
        }
      });
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
  padding: 5px;
  display: flex;
}
.hint {
  text-align: center;
  line-height: 200px;
  font-family: sans-serif;
  font-size: xx-large;
  color: dimgrey;
}
.file-info-row {
  display: flex;
}
.file-info-cell {
  margin-right: 3px;
}
.file-info {
  font-family: sans-serif;
}
</style>


