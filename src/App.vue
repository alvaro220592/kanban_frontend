<template>
  <v-app>
    <v-main>
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
