<template>
  <v-app id="background">
    <v-app-bar app dense color = "#343a40">

    <v-toolbar-title id="title">AGRO</v-toolbar-title>

    <v-spacer></v-spacer>
    <v-btn @click="logout()">
        Salir
      </v-btn>
    </v-app-bar>

    <h1>Crea Cuenta</h1>

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

#background{
  background-color: #16432A;
}

#icon_color{
  background-color: white;
  color: black;
}
</style>

<script>
export default {
    methods: {
    logout() {
      this.$router.push("/home");
    },
  },
}
</script>