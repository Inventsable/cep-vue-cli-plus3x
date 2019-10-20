<template>
  <div class="main-content pt-4">
    <span class="title pa-4" style="color: var(--color-default)">Routing support</span>
    <span class="subheader">Seamlessly switching between HTML pages</span>
  </div>
</template>

<script>
export default {
  data: () => ({
    //
  }),
  computed: {
    app() {
      return this.$root.$children[0];
    }
  },
  mounted() {}
};
</script>

<style>
.main-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
}
</style>
