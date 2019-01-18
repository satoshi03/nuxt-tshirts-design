<template>
  <v-app>
    <v-toolbar
      color="#352525"
      fixed
      app
    >
      <v-btn icon @click="$router.push('/')" class="title-logo">
        <img height="50" src='~/assets/logo.png'></img>
      </v-btn>
    </v-toolbar>
    <v-content>
      <v-container fill-height>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer
      :fixed="fixed"
      app
    >
    <span>&copy; {{ title }} 2019</span>
    </v-footer>
  </v-app>
</template>

<script>
  export default {
    data() {
      return {
        fixed: false,
        title: '君だけのモザT'
      }
    }
  }
</script>

<style>
.title-logo {
  width: 140px;
  padding-top: 5px;
}
</style>

