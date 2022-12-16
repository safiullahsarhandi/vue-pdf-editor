<template>
  <v-snackbar v-if="!!text" v-model="show">
    {{ text }}
  </v-snackbar>
</template>

<script>
export default {
  props: {
    text: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      show: true,
    };
  },
};
</script>

