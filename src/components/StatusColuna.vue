<template>
    <!-- É a coluna de status onde ficam os cards -->
    <v-card class="d-flex flex-column coluna" color="grey-darken-4">
        <slot></slot>

    </v-card>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
    name: 'StatusColuna',

    setup () {
        return {}
    }
})
</script>

<style>
    .coluna {
        min-width: 300px;
        max-width: 300px;
        height: 88vh;
        border-top: 3px orange solid
    }
</style>