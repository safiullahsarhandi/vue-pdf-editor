<template>
  <div class="pdf-document d-flex justify-center" ref="playground">
    <PdfViewer @pages="setPages" />
    <KonvaCanvas
      :width="playgroundWidth"
      :height="playgroundHeight"
      :shapes="shapes"
      :selectedShape="selectedShape"
      @onChange="onCanvasChange"
      @onSelectedShapeChange="onSelectedShapeChange"
      class="overlay"
    />
  </div>
</template>

<script>
import PdfViewer from "./PdfViewer.vue";
import KonvaCanvas from "./KonvaCanvas";
import Konva from "konva";
export default {
  components: {
    PdfViewer,
    KonvaCanvas,
  },

  mounted() {
    this.listenToolbarEvents();
  },
  data() {
    return {
      playgroundWidth: 0,
      playgroundHeight: 0,
      currentY: 0,
      currentX: 0,
      shapes: [],
      selectedShape: "",
      totalPages: 0,
    };
  },
  watch: {
    shapes(shapes) {
      localStorage.setItem("overlay", JSON.stringify(shapes));
    },
  },

  created() {
    window.addEventListener("scroll", this.handleScroll);
    this.setCurrentXY();
  },
  destroyed() {
    window.removeEventListener("scroll", this.handleScroll);
  },
  methods: {
    listenToolbarEvents() {
      this.$root.$on("toolbarEvent", (type) => {
        switch (type) {
          case "rect":
            this.addRectangle();
            break;

          case "circle":
            this.addCircle();
            break;

          case "text":
            this.addText();
            break;

          case "delete":
            this.deleteShape();
            break;

          case "load":
            this.loadOverlay();
            break;

          case "print":
            this.printPdf();
            break;
        }
      });
    },

    handleScroll() {
      const { scrollTop } = document.documentElement;
      if (scrollTop > 200) this.currentY = scrollTop;
    },

    addRectangle() {
      this.shapes.push({
        rotation: 0,
        x: this.currentX,
        y: this.currentY,
        width: 100,
        height: 100,
        fill: Konva.Util.getRandomColor(),
        name: `rect-${Date.now()}`,
        draggable: true,
      });
    },

    addCircle() {
      this.shapes.push({
        rotation: 0,
        x: this.currentX,
        y: this.currentY,
        radius: 50,
        strokeWidth: 4,
        fill: Konva.Util.getRandomColor(),
        name: `circle-${Date.now()}`,
        draggable: true,
      });
    },

    addText() {
      this.shapes.push({
        rotation: 0,
        x: this.currentX,
        y: this.currentY,
        text: "Simple Text",
        fontSize: 30,
        fontFamily: "Calibri",
        fill: Konva.Util.getRandomColor(),
        name: `text-${Date.now()}`,
        draggable: true,
      });
    },

    deleteShape() {
      this.shapes = this.shapes.filter(
        (shape) => shape.name !== this.selectedShape
      );
      this.selectedShape = "";
    },

    loadOverlay() {
      const savedShapes = JSON.parse(localStorage.getItem("overlay"));
      if (savedShapes && savedShapes.length) this.shapes = savedShapes;
    },

    printPdf() {
      window.print();
    },

    setCurrentXY() {
      this.currentY = window.innerHeight / 4;
      this.currentX = window.innerWidth / 2;
    },

    setPages(totalPages) {
      this.totalPages = totalPages;
      this.playgroundHeight = 0;
      this.playgroundWidth = 0;
      // TODO: Improvements needed - Remove Timeout
      setTimeout(this.setPlaygroundDimentions, 100);
    },

    onCanvasChange(shapes) {
      this.shapes = shapes;
    },

    onSelectedShapeChange(name) {
      this.selectedShape = name;
    },
    setPlaygroundDimentions() {
      const { scrollWidth, scrollHeight } = this.$refs.playground;
      this.playgroundHeight = scrollHeight;
      this.playgroundWidth = scrollWidth;
    },
  },
};
</script>



<style lang="css" scoped>
.pdf-document {
  position: relative;
}
.overlay {
  position: absolute;
  top: 0;
  z-index: 1;
}
</style>