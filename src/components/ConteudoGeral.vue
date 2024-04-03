<template>
    <v-app>
		<KanbanBoard>
			<!-- <v-card v-for="status in statuses" :key="status.id" class="d-flex flex-column coluna" color="grey-darken-3" style="border-top: 3px orange solid"> -->
			<StatusColuna v-for="status in statuses" :key="status.id">

				<TituloStatus :statusId="status.id" :statusTitle="status.title" :abrirModalNovaTarefa="openNovaTarefaModal"></TituloStatus>
				
				<div class="overflow-y-auto px-2">
					<CardContainer :data-status_id="status.id" :busca="busca" v-model="status.tasks">
						<CardTarefa 
							v-for="task in status.tasks"
							:key="task.id"
							:data-task_id="task.id"
							:taskTitle="task.title"
							:taskOrder="task.order"
							:bgColor="task.background_color"
						></CardTarefa>
					</CardContainer>
				</div>
			</StatusColuna>
            <!-- </v-card> -->
        </KanbanBoard>
    </v-app>

	<!-- modal -->
	<TarefaModal
		v-model="modalTarefaOpen"
		@emitTarefaSalva="criarTarefa"
		@emitFecharTarefaModal="closeTarefaModal"
	></TarefaModal>

	<!-- toastAtivo -->
	<ToastPopup 
		:toastMessage="'toastMessage'"
		cor="green"
		v-model="toastAtivo"
	></ToastPopup>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import ToastPopup from './ToastPopup.vue'
import TituloStatus from './TituloStatus.vue';
import CardTarefa from './CardTarefa.vue';
import CardContainer from './CardContainer.vue';
import StatusColuna from './StatusColuna.vue'
import KanbanBoard from './KanbanBoard.vue';
import TarefaModal from './Modals/TarefaModal.vue'

import axios from 'axios'
export default defineComponent({
	name: 'ConteudoGeral',
	components: {
		ToastPopup,
		TituloStatus,
		CardTarefa,
		CardContainer,
		StatusColuna,
		KanbanBoard,
		TarefaModal
	},
	setup() {
		const statuses = ref([])
		const modalTarefaOpen = ref(false);
		const novaTarefaStatusId = ref(null)
		const toastAtivo = ref(false)
		const toastMessage = ref('')

		const openNovaTarefaModal = (event) => {
			novaTarefaStatusId.value = event.target.dataset.status_id
			modalTarefaOpen.value = true;
		};

		const criarTarefa = async (dadosEmit) => {
			let status_id = novaTarefaStatusId.value
			
			let req  = await axios.post('http://localhost:8000/api/task', {
				task_status_id: status_id,
				title: dadosEmit.titulo,
				description: dadosEmit.descricao,
				background_color: dadosEmit.corSelecionada
			})

			toastAtivo.value = true
			toastMessage.value = req.data.message
			closeTarefaModal()
			busca()
		}

		const closeTarefaModal = () => {
			novaTarefaStatusId.value = null
			modalTarefaOpen.value = false;
		};

		const busca = async () => {
			let res = await axios.get('http://localhost:8000/api/statuses')
			statuses.value = res.data
		}

		onMounted(() => {
			busca();
		});

		return {
			statuses,
			modalTarefaOpen,
			openNovaTarefaModal,
			closeTarefaModal,
			criarTarefa,
			toastAtivo,
			toastMessage
		};
	},
});
</script>

<style scoped>



</style>