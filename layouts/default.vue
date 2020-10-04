<template>
  <div>
    <v-app-bar 
    dense
    color = "#343a40">
      
    <v-toolbar-title id = "title">AGRO</v-toolbar-title>

    <v-spacer></v-spacer>

    <v-btn v-for="item in items_menu" :key="item.id" :to="item.route" style="margin:3px;">
      <v-list-item-icon>
        <v-icon>{{ item.icon }}</v-icon>
      </v-list-item-icon> 
    </v-btn>

    </v-app-bar>

    <v-main>
      <v-container> <nuxt /> </v-container>
    </v-main>

  </div>
</template>

<style>
#title{
  color: white;
}
</style>

<script>
export default {
  data() {
    return {
      drawer: null,
      items_menu: [
        { id: "01", title: "home", icon: "mdi-cart", route: "/home" },
        { id: "02", title: "login", icon: "mdi-login-variant", route: "/login" },
      ],
    };
  },
  methods: {
    logout() {
      this.$router.push("/");
    },
  },
};
</script>