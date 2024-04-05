<template>
    <h4 class="py-2 px-3 d-flex justify-content-between">
        {{ statusTitle }}

        <v-menu open-on-click transition="slide-y-transition" color="grey-darken-3">
            <template v-slot:activator="{ props }">
                <i class="bi bi-list botao" v-bind="props" style="cursor: pointer"></i>
            </template>

            <!-- Card com as cores disponíveis -->
            <v-card color="grey-darken-3" class="d-flex flex-column" :style="{border: '1px solid #263238'}">
                <span class="menuStatusSpan" :data-status_id="statusId" @click="abrirModalTarefa">Nova tarefa</span>
                <span class="menuStatusSpan" @click="editStatus">Editar</span>
                <span class="menuStatusSpan">Excluir</span>
            </v-card>
        </v-menu>
    </h4>
</template>

<script>
import { defineComponent } from 'vue';

    export default defineComponent({
        name: 'TituloStatus',

        props: {
            statusTitle: String,
            statusId: Number,
            // abrirModalTarefa: Function
        },

        setup (props, { emit }) {

            const abrirModalTarefa = () => {
                emit('emitAbrirModalTarefa', {
                    statusId: props.statusId
                })
            }

            const editStatus = () => {
                emit('emitEditStatus', {
                    statusId: props.statusId,
                    title: props.statusTitle
                })
            }

            return {
                editStatus,
                abrirModalTarefa
            }
        }
    })
</script>

<style>
    .botao {
        color: rgba(212, 212, 212, 0.716);
        transition: .5s;
    }
    .botao:hover {
        transition: .5s;
        color: #fbfbfb;
    }

    .menuStatusSpan {
        padding: 4px 18px;
        cursor: pointer;
    }

    .menuStatusSpan:hover {
        background-color: #d5700b;
    }
</style>