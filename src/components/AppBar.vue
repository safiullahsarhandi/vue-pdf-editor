<template>
  <div>
    <v-app-bar flat app max-height="56" class="d-print-none">
      <v-toolbar-title dense class="text-h5">CF Challenge - PDF Editor 1.0</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-text-field
        dense
        class="shrink mt-4"
        label="PDF URL"
        rounded
        solo
        v-model="pdfUrl"
      ></v-text-field>
      <v-btn
        dense
        rounded
        class="shrink mb-2 ml-2"
        @click="$root.$emit('loadPdf', pdfUrl)"
      >
        Load
      </v-btn>
    </v-app-bar>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pdfUrl: "sample.pdf",
    };
  },
};
</script>



