<template>
  <!-- Example snackbar. Contents can be altered at any time via App.notification -->
  <!-- To show simply toggle status prop at the App level (eg: this.app.notification.status = true;) -->
  <!--  -->
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
  }
};
</script>

