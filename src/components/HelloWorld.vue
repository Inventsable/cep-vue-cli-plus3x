<template>
  <div class="main-content pt-4">
    <span
      class="title pa-4"
      style="width: 100%; display: flex; justify-content: center; color: var(--color-default)"
    >{{msg || defmsg}}</span>
    <v-tooltip bottom>
      <template v-slot:activator="{ on }">
        <v-btn color="primary" dark v-on="on">Button</v-btn>
      </template>
      <span>Tooltip</span>
    </v-tooltip>
    <div class="fake"></div>
    <span>Content way at the bottom to show scrolling</span>
  </div>
</template>

<script>
export default {
  data: () => ({
    msg: ""
  }),
  computed: {
    app() {
      return this.$root.$children[0];
    },
    defmsg() {
      return `${
        this.app.identity ? "Hello " + this.app.identity.extName : null
      }`;
    }
  },
  mounted() {
    this.app.home = this;
  }
};
</script>

<style>
.main-content {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
}

.fake {
  width: 100%;
  height: 1000px;
}
</style>
