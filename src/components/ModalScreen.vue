
<template>
  <!--  -->
  <!-- Sample modal screen. This could be used as an options, licensing, update screen or more -->
  <!--  -->
  <div class="modal-screen">
    <span
      style="width: 100%; display: flex; justify-content: center;"
      class="display-1 mt-3"
    >Whatchu think?</span>
    <div class="animation-header"></div>
    <slottie id="mango" />
    <v-container>
      <v-textarea
        ref="input"
        label="How can we be better?"
        v-model="msg"
        hint="Hint text"
        class="textarea"
        @keyup.enter="submit()"
        hide-details
      ></v-textarea>
      <v-btn
        :disabled="msg.length < 1"
        block
        style="margin-top: 25px;"
        color="primary"
        outlined
        @click="textMe()"
      >Sumbit</v-btn>
    </v-container>
  </div>
</template>

<script>
import slottie from "./slottie.vue";
export default {
  name: "ModalScreen",
  components: {
    slottie
  },
  data: () => ({
    msg: "",
    loaded: false
  }),
  computed: {
    app() {
      return this.$root.$children[0];
    }
  },
  mounted() {
    this.recolorMangoBG();
    //
    // Since this shares the same Vue instance, we have access to everything in App.vue.
    // If you've defined other routes/components as properties or data within App, you
    // have full range of motion between your Modal and Panel extensions and can grab/send data between.
    //
    this.app.modal = this;
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
  },
  methods: {
    init() {
      let self = this;
      this.recolorMangoBG();
    },
    recolorMangoBG() {
      // Restyle our cute Mango note-taker so the background matches Starlette
      let targ = document.getElementById("mango").children[0].children[0]
        .children[1].children[0].children[0].children[0];
      targ.style.fill = this.app.getCSS("color-bg");

      // When using the overlay for the panel, oddly sending the event only works in this section.
      this.stopPanelLoader();

      // Force the input to appear having focus so the user can begin to type instantly.
      this.$nextTick(() => this.$refs.input.focus());
    },
    getTextColor() {
      return this.app.getCSS("color-default");
    },
    // Send an event to our panel over the APPLICATION scope, saying the modal is loaded and stopping the overlay.
    stopPanelLoader() {
      this.app.dispatchEvent(
        `${this.app.identity.extID.match(/.*\./)[0]}modal-loaded`,
        null
      );
    },
    submit() {
      this.textMe();
    },
    textMe() {
      // Send the content inside the textarea back to the panel and close the extension (e.g., return user to panel)
      this.app.dispatchEvent(
        `${this.app.identity.extID.match(/.*\./)[0]}modal-close`,
        this.msg
      );
      // Clear the contents of the modal and any state before closing.
      this.msg = "";
      this.loaded = false;
      // Force the modal extension to close
      this.app.csInterface.closeExtension();
    }
  }
};
</script>

<style>
.modal-screen {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.animation-header {
  width: 100%;
}
</style>