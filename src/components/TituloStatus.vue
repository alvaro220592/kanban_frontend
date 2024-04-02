<template>
    <h4 class="py-2 px-3 d-flex justify-content-between">
        {{ statusTitle }}
        <i :data-status_id="statusId" class="bi bi-plus-circle-fill botao"  style="cursor: pointer" @click="abrirModalNovaTarefa"></i>
    </h4>
</template>

<script>
import { defineComponent } from 'vue';

    export default defineComponent({
        name: 'TituloStatus',

        props: {
            statusTitle: String,
            statusId: Number,
            abrirModalNovaTarefa: Function
        },

        setup () {
            return {
            }
        }
    })
</script>

<style scoped>
    .botao {
        color: rgba(192, 192, 192, 0.716);
        transition: .5s;
    }
    .botao:hover {
        transition: .5s;
        color: #c0c0c0;
    }
</style>