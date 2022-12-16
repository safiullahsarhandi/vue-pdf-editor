<template>
  <v-stage
    :config="{ width, height }"
    @mousedown="handleStageMouseDown"
    @touchstart="handleStageMouseDown"
  >
    <v-layer>
      <div v-for="shape in shapes" :key="shape.name">
        <v-rect
          v-if="isRectangle(shape)"
          :config="shape"
          @dragend="handleTransformEnd"
          @transformend="handleTransformEnd"
        />
        <v-circle
          v-if="isCircle(shape)"
          :config="shape"
          @dragend="handleTransformEnd"
          @transformend="handleTransformEnd"
        />
        <v-text
          v-if="isText(shape)"
          :config="shape"
          @dragend="handleTransformEnd"
          @transformend="handleTransformEnd"
          @dblclick="handleText"
        />
      </div>
      <v-transformer ref="transformer" />
    </v-layer>
  </v-stage>
</template>

<script>
export default {
  props: {
    width: {
      type: Number,
      default: 0,
    },
    height: {
      type: Number,
      default: 0,
    },
    shapes: {
      type: Array,
      default: () => [],
    },
    selectedShape: {
      type: String,
      default: "",
    },
  },

  watch: {
    selectedShape(name) {
      this.updateTransformer(name);
    },
  },

  methods: {
    isRectangle(shape) {
      return shape.name.includes("rect");
    },
    isCircle(shape) {
      return shape.name.includes("circle");
    },
    isText(shape) {
      return shape.name.includes("text");
    },

    handleTransformEnd(e) {
      const localShapes = this.shapes.map((shape) => {
        if (shape.name === this.selectedShape) {
          return { ...shape, ...e.target.attrs };
        }
        return shape;
      });

      this.$emit("onChange", localShapes);
    },

    handleStageMouseDown(e) {
      //Ignore Native Events
      if (!e.evt) {
        return;
      }

      // Clear Selection on Stage Events
      if (e.target === e.target.getStage()) {
        this.$emit("onSelectedShapeChange", "");
        this.updateTransformer();
        return;
      }

      // Ignore Transformer Events
      const clickedOnTransformer =
        e.target.getParent().className === "Transformer";
      if (clickedOnTransformer) {
        return;
      }

      // Look for Shape Events
      const shapeName = e.target.name();
      const shape = this.shapes.find((shape) => shape.name === shapeName);
      if (shape) {
        this.$emit("onSelectedShapeChange", shapeName);
      } else {
        this.$emit("onSelectedShapeChange", "");
      }

      this.updateTransformer(shapeName);
    },

    updateTransformer(shapeName) {
      const transformerNode = this.$refs.transformer.getNode();
      const stage = transformerNode.getStage();
      const selectedNode = stage.findOne("." + shapeName);
      selectedNode
        ? transformerNode.nodes([selectedNode])
        : transformerNode.nodes([]);
    },

    handleText(e) {
      // Hide Text Shape
      e.target.hide();

      // Get Text Shape Position
      const textPosition = e.target.getAbsolutePosition();

      const stageBox = e.target.getStage().container().getBoundingClientRect();

      const areaPosition = {
        x: stageBox.left + textPosition.x,
        y: stageBox.top + textPosition.y,
      };

      // Create Text Field Input in DOM
      const textarea = document.createElement("textarea");
      document.body.appendChild(textarea);

      // Style Text Field Input According to Text Field Shape
      textarea.value = e.target.text();
      textarea.style.position = "absolute";
      textarea.style.top = areaPosition.y + "px";
      textarea.style.left = areaPosition.x + "px";
      textarea.style.width = e.target.width() - e.target.padding() * 2 + "px";
      textarea.style.height =
        e.target.height() - e.target.padding() * 2 + 5 + "px";
      textarea.style.fontSize = e.target.fontSize() + "px";
      textarea.style.border = "none";
      textarea.style.padding = "0px";
      textarea.style.margin = "0px";
      textarea.style.overflow = "hidden";
      textarea.style.background = "none";
      textarea.style.outline = "none";
      textarea.style.resize = "none";
      textarea.style.lineHeight = e.target.lineHeight();
      textarea.style.fontFamily = e.target.fontFamily();
      textarea.style.transformOrigin = "left top";
      textarea.style.textAlign = e.target.align();
      textarea.style.color = e.target.fill();

      // Text Field Input Autofocus
      textarea.focus();

      // On Text Field Input Focus Removed
      textarea.addEventListener("blur", () => {
        e.target.text(textarea.value);
        document.body.removeChild(textarea);
        e.target.show();
        textarea.removeEventListener("blur", null);
      });
    },
  },
};
</script>
