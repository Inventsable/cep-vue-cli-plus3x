<template>
  <v-navigation-drawer :style="clipTop()" v-model="open" absolute dark temporary clipped>
    <v-list style="background-color: red;">
      <v-list-item v-for="item in items" :key="item.title" link>
        <v-list-item-icon>
          <v-icon>{{ item.icon }}</v-icon>
        </v-list-item-icon>

        <v-list-item-content>
          <v-list-item-title>{{ item.title }}</v-list-item-title>
        </v-list-item-content>
      </v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>

<script>
export default {
  name: "drawer",
  data: () => ({
    open: false,
    items: [
      { title: "Dashboard", icon: "dashboard" },
      { title: "Account", icon: "account_box" },
      { title: "Admin", icon: "gavel" }
    ]
  }),
  mounted() {
    this.app.drawer = this;
  },
  computed: {
    app() {
      return this.$root.$children[0];
    }
  },
  methods: {
    clipTop() {
      return `
        margin-top: 48px;
        height: calc(100vh - 50px);
        `;
    }
  }
};
</script>

<style>
</style>