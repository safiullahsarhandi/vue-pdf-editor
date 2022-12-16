<template>
  <div>
    <PdfPage
      :pdfData="pdfData"
      v-for="page in pages"
      :key="page + Date.now()"
      :currentPage="page"
    />
    <AppSnackbar :text="snackbarText" />
  </div>
</template>

<script>
import pdf from "pdfvuer";
import PdfPage from "./PdfPage.vue";
import AppSnackbar from "./AppSnackbar";
export default {
  components: {
    PdfPage,
    AppSnackbar,
  },
  data() {
    return {
      pdfData: null,
      pages: null,
      snackbarText: "",
    };
  },

  mounted() {
    this.$root.$on("loadPdf", (pdfUrl) => {
      this.getPdfData(pdfUrl);
    });
  },

  created() {
    this.getPdfData("sample.pdf");
  },

  methods: {
    getPdfData(pdfUrl) {
      this.snackbarText = "";
      this.pdfData = pdf.createLoadingTask(pdfUrl);
      this.pdfData
        .then((pdf) => {
          this.pages = pdf.numPages;
          this.$emit("pages", this.pages);
        })
        .catch(
          () =>
            (this.snackbarText =
              "Unable to load Pdf File. Make sure the URL is correct.")
        );
    },
  },
};
</script>

