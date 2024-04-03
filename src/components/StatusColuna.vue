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
        min-width: 250px;
        max-width: 250px;
        height: 90vh;
        border-top: 3px orange solid
    }
</style>