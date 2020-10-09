<template>
  <v-app>
    <v-app-bar dense color = "#343a40">

    <v-toolbar-title id="title">AGRO</v-toolbar-title>

    <v-spacer></v-spacer>

   <v-tooltip bottom v-for="item in items_menu" :key="item.id" color="light-primary">
      <template v-slot:activator="{ on, attrs }">
        <v-btn v-on="on" v-bind="attrs" icon :to="item.route" style="margin:5px;">
          <v-icon id="icon_color">{{ item.icon }}</v-icon>
        </v-btn>
      </template>
      <span>{{item.name}}</span>
    </v-tooltip>

    </v-app-bar>

    <v-main>
      <v-container>
        <nuxt/>
      </v-container>
    </v-main>

  </v-app>
</template>

<style>
#title{
  color: white;
}

#icon_color{
  background-color: white;
  color: black;
}
</style>

<script>
export default {
  data() {
    return {
      drawer: null,
      items_menu: [
        { id: "01", title: "home", icon: "mdi-cart", route: "/home", name:"Inicio"},
        { id: "02", title: "login", icon: "mdi-login-variant", route: "/login", name:"Iniciar Sesion"},
        { id: "03", title: "register", icon: "mdi-account", route: "/user-register", name:"Registro"},
      ],
    };
  },
};
</script>