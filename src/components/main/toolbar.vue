<template>
  <!-- New-gen redressing of navbar using Vuetify's new distinct app-bar component -->
  <v-app-bar
    :color="this.realColor"
    style="position: absolute; z-index: 3;"
    dark
    dense
    :absolute="true"
    flat
  >
    <v-app-bar-nav-icon @click="openDrawer()"></v-app-bar-nav-icon>
    <v-toolbar-title style="user-select: none; cursor: default;">{{this.$route.name}}</v-toolbar-title>
    <v-spacer></v-spacer>
    <v-btn text v-for="(item, i) in items" :key="i" @click="checkAction(item)">
      <v-icon v-if="item.route" :color="isActiveRoute(item.routename)">{{item.icon}}</v-icon>
      <v-icon v-else>{{item.icon}}</v-icon>
    </v-btn>
  </v-app-bar>
</template>

<script>
export default {
  name: "toolbar",
  props: {
    color: {
      type: String,
      default: "color-header"
    }
  },
  data: () => ({
    items: [
      {
        icon: "home",
        route: true,
        routename: "home"
      },
      {
        icon: "mdi-comment",
        route: true,
        routename: "about"
      },
      {
        icon: "mdi-account-circle"
      }
    ]
  }),
  mounted() {
    this.app.toolbar = this;
    document.querySelectorAll(".theme--dark.v-sheet").forEach(item => {
      item.style.backgroundColor = this.realColor;
    });
  },
  computed: {
    app() {
      return this.$root.$children[0];
    },
    // Defer to passed prop as bg color else be transparent
    realColor() {
      if (this.color) {
        return /color/.test(this.color)
          ? this.app.getCSS(this.color)
          : this.color;
      } else {
        return this.app.getCSS("color-bg");
      }
    }
  },
  methods: {
    openDrawer() {
      if (this.app.drawer) this.app.drawer.open = true;
    },
    checkAction(item) {
      if (item.route) {
        this.goToRoute(item.routename);
      } else {
        console.log("Hello");
        this.app.launchModal();
      }
    },
    isActiveRoute(target) {
      if (target == this.$route.name) {
        return this.app.getCSS("color-selection");
      } else {
        return null;
      }
    },
    goToRoute(name) {
      name !== this.$route.name ? this.$router.push({ name: name }) : null;
    },
    checkRoute(name) {
      return name == this.$route.name;
    }
  }
};
</script>

<style>
</style>