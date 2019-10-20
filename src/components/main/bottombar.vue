<template>
  <!-- Updated, simpler version.vue -->
  <div class="bottomBar" :style="getBottomBarStyle()">
    <span class="version-id">{{version}}</span>
  </div>
</template>

<script>
export default {
  name: "bottombar",
  data: () => ({
    height: "30px"
  }),
  props: {
    dark: Boolean
  },
  computed: {
    app() {
      return this.$root.$children[0];
    },
    version() {
      return this.app.identity ? this.app.identity.extVersion : null;
    }
  },
  methods: {
    getBottomBarStyle() {
      return `
        height: ${this.height};
        background-color: var(--color-${this.dark ? "header" : "bg"});`;
    }
  }
};
</script>

<style>
.bottomBar {
  z-index: 3;
  position: absolute;
  bottom: 0px;
  display: flex;
  justify-content: flex-end;
  padding: 0px 10px;
  align-items: center;
  user-select: none;
  width: 100%;
  border: 0px;
}

.version-id {
  letter-spacing: 0.75ch;
  cursor: default;
}

.leftBar {
  display: flex;
  justify-content: flex-start;
  flex-wrap: nowrap;
  align-items: center;
}
</style>
