<template>
  <ModalScreen />
</template>

<script>
import ModalScreen from "@/components/ModalScreen.vue";

export default {
  name: "modal",
  components: {
    ModalScreen
  },
  computed: {
    app() {
      return this.$root.$children[0];
    }
  },
  mounted() {
    let self = this;
    this.app.csInterface.addEventListener(
      "com.adobe.csxs.events.WindowVisibilityChanged",
      function(evt) {
        this.app.registerMsg(evt);
      }
    );
  }
};
</script>
