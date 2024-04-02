<template>
    <h4 class="p-2 d-flex justify-content-between">
        {{ statusTitle }}
        <i :data-status_id="statusId" class="bi bi-plus-circle"  style="cursor: pointer" @mouseover="iconOver" @mouseleave="iconLeave" @click="abrirModalNovaTarefa"></i>
    </h4>
</template>

<script>
import { defineComponent } from 'vue';

    export default defineComponent({
        name: 'StatusTitle',

        props: {
            statusTitle: String,
            statusId: Number,
            abrirModalNovaTarefa: Function
        },

        setup () {
            
            const iconOver = (event) => {
                event.target.classList.remove('bi-plus-circle')
                event.target.classList.add('bi-plus-circle-fill')
            }

            const iconLeave = (event) => {
                event.target.classList.remove('bi-plus-circle-fill')
                event.target.classList.add('bi-plus-circle')
            }

            return {
                iconLeave,
                iconOver
            }
        }
    })
</script>