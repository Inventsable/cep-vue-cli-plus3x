<template>
  <v-app>
    <toolbar color="color-input" v-if="notModal" />
    <identity />
    <menus />
    <drawer />

    <notification />
    <loadingscreen />
    <bottombar v-if="notModal" :dark="true" />
    <v-content :style="getContentStyle()">
      <v-overlay :value="overlay">
        <v-progress-circular indeterminate size="64"></v-progress-circular>
      </v-overlay>
      <router-view />
    </v-content>
  </v-app>
</template>

<script>
// Dynamic CSS variables that automatically handle all app themes and changes:
// https://github.com/Inventsable/starlette
import starlette from "starlette";

// Utility components
// https://github.com/Inventsable/cep-vue-cli-router2x#components
import identity from "./components/main/identity.vue";
import menus from "./components/main/menus.vue";

// Optional components
import loadingscreen from "./components/main/loadingscreen.vue";
import bottombar from "./components/main/bottombar.vue";
import toolbar from "./components/main/toolbar.vue";
import notification from "./components/main/notification.vue";
import drawer from "./components/main/drawer.vue";

export default {
  name: "App",
  components: {
    identity,
    menus,
    // optional
    toolbar,
    bottombar,
    loadingscreen,
    drawer,
    notification
  },
  computed: {
    storage() {
      return window.localStorage;
    },
    notModal() {
      return this.identity ? !/modal/.test(this.identity.extID) : null;
    }
  },
  data: () => ({
    // required
    csInterface: null,
    isMounted: false,
    identity: null,
    menus: null,
    // routes
    home: null,
    // optional components
    toolbar: null,
    loadingscreen: null,
    overlay: false,
    modal: null,
    notification: null
  }),
  mounted() {
    console.clear();
    starlette.init();
    this.csInterface = new CSInterface();
    this.csInterface.addEventListener("console", this.consoler);

    // Utility components already mounted prior to this
    console.log(
      `${this.identity.extName} ${this.identity.extVersion} : ${
        this.identity.isDev ? "DEV" : "BUILD"
      }`
    );
    this.isMounted = true;
    let self = this;

    // @@@ REMOVE THIS FROM TEMPLATE
    if (!this.notModal) {
      this.$router.push({ name: "modal" });
      // this.modal.loaded = true;
      // this.modal.recolorMangoBG();
      this.modal.init();
    } else {
      this.csInterface.addEventListener(
        `${this.identity.extID.match(/.*\./)[0]}modal-close`,
        evt => {
          self.notification.show();
          self.home.msg = evt.data;
        }
      );
      this.csInterface.addEventListener(
        `${this.identity.extID.match(/.*\./)[0]}modal-loaded`,
        evt => {
          self.overlay = false;
        }
      );
    }

    this.loadUniversalScripts();
  },
  methods: {
    launchModal() {
      this.overlay = true;
      this.csInterface.requestOpenExtension(
        `${this.identity.extID.match(/.*\./)[0]}modal`,
        ""
      );
    },
    dispatchEvent(name, data) {
      var event = new CSEvent(name, "APPLICATION");
      event.data = data;
      this.csInterface.dispatchEvent(event);
    },
    getContentStyle() {
      return this.notModal
        ? `
          max-height: calc(100vh - 78px);
          overflow-y: auto;
          margin-top: 48px;
          margin-bottom: 30px;`
        : `max-height: calc(100vh);
          overflow-y: auto;
          margin-top: 0px;
          margin-bottom: 0px;`;
    },
    loadScript(path) {
      // Correctly loads a script regardless of whether Animate or regular CEP app
      if (!/FLPR/.test(this.identity.appName))
        this.csInterface.evalScript(`$.evalFile('${path}')`);
      else
        this.csInterface.evalScript(
          `fl.runScript(FLfile.platformPathToURI("${path}"))`
        );
    },
    loadUniversalScripts() {
      // Preloads any script located inside ./src/host/universal
      let utilFolder = window.cep.fs.readdir(
        `${this.identity.root}/src/host/universal/`
      );
      if (!utilFolder.err) {
        let valids = utilFolder.data.filter(file => {
          return /\.(jsx|jsfl)$/.test(file);
        });
        valids.forEach(file => {
          this.loadScript(`${this.identity.root}/src/host/universal/${file}`);
        });
      }
      // Preloads any script located in ./src/host/[appName]/
      let hostFolder = window.cep.fs.readdir(
        `${this.identity.root}/src/host/${this.identity.appName}/`
      );
      if (!hostFolder.err) {
        let valids = hostFolder.data.filter(file => {
          return /\.(jsx|jsfl)$/.test(file);
        });
        valids.forEach(file => {
          this.loadScript(
            `${this.identity.root}/src/host/${this.identity.appName}/${file}`
          );
        });
      } else {
        console.log(
          `${this.identity.root}/src/host/${this.identity.appName} has no valid files or does not exist`
        );
      }
    },
    consoler(msg) {
      // Catches all console.log() usage in .jsx files via CSEvent
      console.log(`${this.identity.appName}: ${msg.data}`);
    },
    getCSS(prop) {
      // Returns current value of CSS variable
      // prop == typeof String as name of variable, with or without leading dashes:
      // this.getCSS('color-bg') || this.getCSS('--scrollbar-width')
      return window
        .getComputedStyle(document.documentElement)
        .getPropertyValue(`${/^\-\-/.test(prop) ? prop : "--" + prop}`);
    },
    setCSS(prop, data) {
      // Sets value of CSS variable
      // prop == typeof String as name of variable, with or without leading dashes:
      // this.setCSS('color-bg', 'rgba(25,25,25,1)') || this.setCSS('--scrollbar-width', '20px')
      document.documentElement.style.setProperty(
        `${/^\-\-/.test(prop) ? prop : "--" + prop}`,
        data
      );
    }
  }
};
</script>

<style>
/* Various helper styles to match host application's theme */
@import url("https://fonts.googleapis.com/css?family=Open+Sans&display=swap");
:root {
  --quad: cubic-bezier(0.48, 0.04, 0.52, 0.96);
  --quart: cubic-bezier(0.76, 0, 0.24, 1);
  --quint: cubic-bezier(0.84, 0, 0.16, 1);
  --toolbar-height: 48px;
  --bottombar-height: 30px;
  /*  */
  background-color: var(--color-bg);
  color: var(--color-default);
  font-family: "Open Sans", sans-serif;
}

body::-webkit-scrollbar {
  width: 0px;
}
#app::-webkit-scrollbar {
  display: block;
}
::-webkit-scrollbar {
  background-color: var(--color-scrollbar);
  width: 16px;
}
::-webkit-scrollbar-thumb {
  background: var(--color-scrollbar-thumb);
  border-radius: 20px;
}
::-webkit-scrollbar-thumb:hover {
  background: var(--color-scrollbar-thumb-hover);
}
::-webkit-scrollbar-resizer {
  display: none;
}
::-webkit-scrollbar-button {
  height: 0px;
}

/* Various ways of overriding Vuetify's default component colors to match Starlette */
/* Rather annoyingly you need to use !important tags for colors since Vuetify 2.0 */
.theme--light.v-application,
.theme--dark.v-navigation-drawer {
  background-color: var(--color-bg) !important;
  color: var(--color-default) !important;
}

.theme--light.v-input:not(.v-input--is-disabled) input,
.theme--light.v-input:not(.v-input--is-disabled) textarea,
.v-input--is-label-active label,
.theme--light.v-label,
.theme--light.v-messages,
.theme--dark.v-list-item:not(.v-list-item--active):not(.v-list-item--disabled) {
  color: var(--color-default) !important;
}

.theme--light.v-text-field > .v-input__control > .v-input__slot:before {
  border-color: var(--color-default) !important;
}

.theme--light.v-btn.v-btn--disabled {
  color: var(--color-btn-disabled-text) !important;
}

.theme--dark.v-sheet:not(.v-list) {
  background-color: var(--color-header) !important;
}

.theme--dark.v-sheet:not(.v-toolbar) {
  background-color: var(--color-bg) !important;
}
.theme--dark.v-sheet {
  color: var(--color-default) !important;
}

.v-btn--outlined .v-btn__content .v-icon,
.v-btn--round .v-btn__content .v-icon,
.theme--dark.v-icon {
  color: var(--color-default) !important;
}

.v-tooltip__content {
  background: var(--color-header) !important;
  color: var(--color-default) !important;
}
</style>
