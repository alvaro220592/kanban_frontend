<template>
    <v-card class="board pa-4 h-screen d-flex flex-row overflow-x-auto gap-2 w-100 rounded-0" color="blue-darken-4">
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
	width: 98%;
	display: flex;
	flex-direction: row;
	gap: 10px;
	overflow-x: auto;
	height: 95vh;
	padding: 10px;
}
</style>