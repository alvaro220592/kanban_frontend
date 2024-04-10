<template>
    <v-card class="board  d-flex flex-row overflow-x-auto gap-2 rounded-0" elevation="0" color="transparent">
        <slot></slot>
    </v-card>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
    name: 'KanbanBoard',

    setup () {
        return {}
    }
})
</script>

<style>
.board {
    margin: 0px auto;
    width: 100%;
    height: 90vh;
}
</style>