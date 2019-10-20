<template>
  <div class="main-content pt-4">
    <span class="title pa-4">Routing support</span>
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
  }
};
</script>

<style>
.main-content {
  color: var(--color-default);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  width: 100%;
}
</style>
