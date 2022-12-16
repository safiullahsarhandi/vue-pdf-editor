<template>
  <div>
    <pdf :src="pdfData" :page="currentPage"  :scale.sync="scale">
      <template slot="loading"> Page is loading.... </template>
    </pdf>
    <div class="mb-3 d-print-none"></div>
  </div>
</template>

<script>
import pdf from "pdfvuer";
export default {
  name: "PdfViewer",
  components: {
    pdf,
  },
  props: {
    pdfData: {
      required: true,
    },
    currentPage: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      scale: 1.5,
    };
  },
};
</script>

