<template>
  <!--  -->
  <!-- Sample modal view. I like further abstracting views from components 
  in case I want to easily add toolbars or other features-->
  <!--  -->
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
    //
    // This is meant to send an event to the panel when the modal closes (when X is pressed)
    this.app.csInterface.addEventListener(
      "com.adobe.csxs.events.WindowVisibilityChanged",
      //
      // Results are unreliable, it's best not to freeze or rely on the modal's state in the event
      // the user might manually close it rather than let you programmatically do so.
      evt => {
        this.app.dispatchEvent(
          `${this.app.identity.extID.match(/.*\./)[0]}modal-close`,
          null
        );
      }
    );
  }
};
</script>
