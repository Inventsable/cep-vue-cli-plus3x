<template>
  <v-snackbar
    v-if="notModal"
    v-model="status"
    top
    :timeout="timeout"
    :color="color"
    style="z-index: 3001;"
  >
    {{text}}
    <v-btn text @click="status = false">Close</v-btn>
  </v-snackbar>
</template>

<script>
export default {
  name: "notification",
  data: () => ({
    timeout: 2000,
    status: false,
    text: "Saved!",
    color: "primary "
  }),
  mounted() {
    console.log("Snackbar mounted");
    this.app.notification = this;
  },
  computed: {
    app() {
      return this.$root.$children[0];
    },
    notModal() {
      return this.app ? this.app.notModal : false;
    }
  },
  methods: {
    show() {
      this.status = true;
    },
    hide() {
      this.status = false;
    }
    // noteStyle() {
    //   return `
    //     z-index: 1001;
    //   `;
    // }
  }
};
</script>

