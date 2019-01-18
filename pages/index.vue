<template>
  <v-layout
    row
    fill-height
    justify-center
    align-center>
    <v-flex
      xs12
      sm8
      md6
      >
      <div class="text-xs-center">
        <v-img :aspect-ratio="16/6" transition="slide-x-reverse-transition" max-width="600px" :src='require("~/assets/logo.png")' />
        <p class="description">１分で好きな絵柄のモザイクTシャツを作れます</p>
        <v-btn round large color="orange darken-4" dark @click="$router.push('/maker')">作る(無料)</v-btn>
      </div>
    </v-flex>
  </v-layout>
</template>

<style>
.logo {
  width: 300px;
}

.description {
  font-size: 20px;
  color: #222222;
  font-weight: 600;
}

.search-box {
  padding: 20px;
  padding-bottom: 5px;
}
</style>

