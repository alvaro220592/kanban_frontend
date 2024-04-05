<template>    
        <VueDraggable
            class="cardContainer d-flex flex-column gap-2"
            group="statuses"
            animation="250"
            ghostClass="ghost"
            @dragover="handleDragOver"
            @dragleave="handleDragLeave"
            @end="drop"
        >
            <slot></slot>

        </VueDraggable>
</template>

<script>
import { VueDraggable } from 'vue-draggable-plus';
import axios from 'axios'
import { defineComponent } from 'vue';

export default defineComponent ({
    components: {
        VueDraggable
    },
    
    props: {
        busca: Function
    },

    setup (props) {

        const handleDragOver = (event) => {
			let cardContainer = event.target.closest('.cardContainer')

			if (cardContainer) {
				cardContainer.classList.add('cardContainerBorder')
			}
		}

		const handleDragLeave = () => {
			removerBordas()
		}

        const drop = async (event) => {
			// Dados da coluna antiga
			let status_to = event.to
			let children_status_to = await status_to.children;
			let tasks_status_to = []

			// Dados da coluna nova
			let status_from = event.from
			let children_status_from = await status_from.children;
			let tasks_status_from = []

			// Construindo o array que registra os cards da coluna antiga
			for (let i = 0; i < children_status_to.length; i++) {
				tasks_status_to.push({
					id: children_status_to[i].dataset.task_id,
					order: i
				})
			}

			// Construindo o array que registra os cards da coluna nova
			for (let i = 0; i < children_status_from.length; i++) {
				tasks_status_from.push({
					id: children_status_from[i].dataset.task_id,
					order: i
				})
			}

			await axios.post('http://localhost:8000/api/changeTaskStatus', {
				status_to_id: status_to.dataset.status_id,
				tasks_status_to: tasks_status_to,
				status_from_id: status_from.dataset.status_id,
				tasks_status_from: tasks_status_from
			})

			removerBordas()			

			props.busca
		}

        const removerBordas = () => {
			let cardContainers = document.querySelectorAll('.cardContainer')
			cardContainers.forEach(item => {
				item.classList.remove('cardContainerBorder')
			})
		}

        return {
            handleDragOver,
            handleDragLeave,
            drop
        }
    }
})
</script>

<style>
    .cardContainerBorder {
        /* border: 2px dashed #9b95a5; */
		border: 2px dashed #ffc787;
        background-color: transparent;
        padding: 2px 2px;
    }

    .ghost {
        opacity: 0;
        background-color: #f4f4f4;
    }
</style>