<template>
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
  watch: {
    loaded(state) {
      if (state) {
        this.app.dispatchEvent(
          `${this.app.identity.extID.match(/.*\./)[0]}modal-loaded`,
          null
        );
      }
    }
  },
  mounted() {
    this.recolorMangoBG();
    this.app.modal = this;
    let self = this;
    csInterface.addEventListener(
      "com.adobe.csxs.events.WindowVisibilityChanged",
      evt => {
        this.app.dispatchEvent(
          `${this.app.identity.extID.match(/.*\./)[0]}modal-close`,
          null
        );
      }
    );
  },
  methods: {
    recolorMangoBG() {
      let targ = document.getElementById("mango").children[0].children[0]
        .children[1].children[0].children[0].children[0];
      targ.style.fill = this.app.getCSS("color-bg");
      this.stopPanelLoader();
      this.$nextTick(() => this.$refs.input.focus());
    },
    getTextColor() {
      return this.app.getCSS("color-default");
    },
    init() {
      let self = this;
      this.recolorMangoBG();
    },
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
      let self = this;
      this.app.dispatchEvent(
        `${this.app.identity.extID.match(/.*\./)[0]}modal-close`,
        self.msg
      );
      this.msg = "";
      this.loaded = false;
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