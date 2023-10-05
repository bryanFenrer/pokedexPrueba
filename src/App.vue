<template>
  <v-app>
    <v-navigation-drawer app>
      <v-list>
        <v-list-item link to="/">
          <v-list-item-icon>
            <v-icon>mdi-pokeball</v-icon>
          </v-list-item-icon>
          <v-list-item-title>Pokedex</v-list-item-title>
        </v-list-item>
        <v-list-item link to="/profile">
          <v-list-item-icon>
            <v-icon>mdi-account</v-icon>
          </v-list-item-icon>
          <v-list-item-title>Profile</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar app color="indigo darken-4" dark>
      <v-toolbar-title>Pokedex App</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <router-view></router-view>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App',
}
</script>
<style scoped>
/* Estilos específicos para la barra de navegación */
.v-toolbar {
  background-color: indigo darken-4 !important;
}
</style>
