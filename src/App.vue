<template>
  <v-app>
    <v-main color="red-darken-4">
      <ConteudoGeral></ConteudoGeral>
    </v-main>
  </v-app>
</template>

<script>
import ConteudoGeral from './components/ConteudoGeral.vue'

export default {
  name: 'App',

  components: {
    ConteudoGeral,
  },

  data: () => ({
    //
  }),
}
</script>

<style>
html{
  overflow-y: hidden;
}
</style>
