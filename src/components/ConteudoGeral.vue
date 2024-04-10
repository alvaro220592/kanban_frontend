<template>
	<v-app>
		<v-card class="d-block h-screen rounded-0" color="blue-darken-4">
			DEIXAR PRE PREENCHIDO O CAMPO DE EDICAO DE STATUS
			<v-container>
				<h1>Kanban</h1>
				<KanbanBoard>
					<StatusColuna v-for="status in statuses" :key="status.id">

						<TituloStatus
							:statusId="status.id"
							:statusTitle="status.title"
							@emitAbrirModalTarefa="openTarefaModal"
							@emitEditStatus="editStatus"
							@emitExcluirStatus="excluirItem"
						></TituloStatus>

						<div class="overflow-y-auto px-2">
							<CardContainer :data-status_id="status.id" :busca="busca" v-model="status.tasks">
								<CardTarefa 
									v-for="task in status.tasks"
									:key="task.id"
									:taskId="task.id"
									:data-task_id="task.id"
									:taskTitle="task.title"
									:taskDescription="task.description"
									:taskCreatedAt="task.created_at"
									:taskOrder="task.order"
									:bgColor="task.background_color"
									:taskPriorityName="task.priority.name"
									:taskPriorityId="task.priority.id"
									@emitEditTarefa="editTarefa"
									@emitExcluirTarefa="excluirItem"
								>
								</CardTarefa>
							</CardContainer>
						</div>
					</StatusColuna>

					<v-tooltip text="Nova coluna">
						<template v-slot:activator="{ props }">
							<h1><i v-bind="props" class="bi bi-plus-circle cursor-pointer botao" @click="abrirColunaModal"></i></h1>
						</template>
					</v-tooltip>

				</KanbanBoard>
			</v-container>
		</v-card>
	</v-app>

	<Tarefa2Modal 
		v-model="Tarefa2ModalOpen"
		@emitFecharTarefaModal="closeTarefaModal"
		@emitSalvarTarefa="salvarTarefa"
		:priorities="priorities"
		:valorInicialTituloTarefa="valorInicialTituloTarefa"
		:valorInicialDescricaoTarefa="valorInicialDescricaoTarefa"
		:valorInicialPrioridadeIdTarefa="valorInicialPrioridadeIdTarefa"
		:valorInicialCorTarefa="valorInicialCorTarefa"
	>
	</Tarefa2Modal>

	<ColunaModal 
		v-model="modalColunaOpen"
		@emitSalvarStatus="salvarStatus"
		@emitFecharColunaModal="closeColunaModal"
		:valorInicialTituloStatus="valorInicialTituloStatus">
	</ColunaModal>

	<ConfirmExclusaoModal
		v-model="ConfirmExclusaoModalShow"
		:funcaoChamada="confirmarExclusao"
	>

	</ConfirmExclusaoModal>

	<!-- toastAtivo -->
	<ToastPopup :toastMessage="toastMessage" :cor="'green'" v-model="toastAtivo"></ToastPopup>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import ToastPopup from './ToastPopup.vue'
import TituloStatus from './TituloStatus.vue';
import CardTarefa from './CardTarefa.vue';
import CardContainer from './CardContainer.vue';
import StatusColuna from './StatusColuna.vue'
import KanbanBoard from './KanbanBoard.vue';
// import TarefaModal from './Modals/TarefaModal.vue'
import Tarefa2Modal from './Modals/Tarefa2Modal.vue'
import ColunaModal from './Modals/ColunaModal.vue'
import ConfirmExclusaoModal from './Modals/ConfirmExclusaoModal.vue'

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
		// TarefaModal,
		ColunaModal,

		Tarefa2Modal,
		ConfirmExclusaoModal
	},
	setup() {
		const statuses = ref([])
		const priorities = ref([])
		const modalTarefaOpen = ref(false);
		const novaTarefaStatusId = ref(null)
		const toastAtivo = ref(false)
		const toastMessage = ref('')
		const modalColunaOpen = ref(false);
		const valorInicialTituloStatus = ref('');
		const Tarefa2ModalOpen = ref(false)
		const statusId = ref(null)

		const valorInicialTituloTarefa = ref('')
		const valorInicialDescricaoTarefa = ref('')
		const valorInicialPrioridadeIdTarefa = ref('')
		const valorInicialCorTarefa = ref('')
		const taskId = ref(null)

		const editandoTarefa = ref(false)
		const editandoStatus = ref(false)

		const ConfirmExclusaoModalShow = ref(false)
		const itemParaExcluir = ref(null)

		const openTarefaModal = (dadosEmit) => {
			if (dadosEmit.modoEdicao == false) {
				valorInicialTituloTarefa.value = ''
				valorInicialDescricaoTarefa.value = ''
				valorInicialPrioridadeIdTarefa.value = ''
				valorInicialCorTarefa.value = ''
				taskId.value = null

				editandoTarefa.value = false
			} else {
				editandoTarefa.value = true
			}

			novaTarefaStatusId.value = dadosEmit.statusId
			Tarefa2ModalOpen.value = true
		};

		const salvarTarefa = async (dadosEmit) => {

			let status_id = novaTarefaStatusId.value

			let url = 'http://localhost:8000/api/task'
			let opcoes = {
				task_status_id: status_id,
				title: dadosEmit.titulo,
				description: dadosEmit.descricao,
				background_color: dadosEmit.corSelecionada,
				priority_id: dadosEmit.prioridadeId
			}
			
			if (editandoTarefa.value == true) {
				url = `http://localhost:8000/api/task/update/${taskId.value}`
				opcoes = {
					// task_status_id: status_id,
					title: dadosEmit.titulo,
					description: dadosEmit.descricao,
					background_color: dadosEmit.corSelecionada,
					priority_id: dadosEmit.prioridadeId
				}
			}

			let req = await axios.post(url, opcoes)

			toastAtivo.value = true
			toastMessage.value = req.data.message
			closeTarefaModal()
			busca()
		}

		const closeTarefaModal = () => {
			novaTarefaStatusId.value = null
			Tarefa2ModalOpen.value = false;
			taskId.value = null

			valorInicialTituloTarefa.value = null
			valorInicialDescricaoTarefa.value = null
			valorInicialPrioridadeIdTarefa.value = null
			valorInicialCorTarefa.value = null
		};

		const abrirColunaModal = () => {
			modalColunaOpen.value = true
		}

		const salvarStatus = async (dadosEmit) => {
			
			let url = 'http://localhost:8000/api/status/store'
			let opcoes = {
				title: dadosEmit.titulo
			}

			if (editandoStatus.value == true) {
				url = `http://localhost:8000/api/status/update/${statusId.value}`
				opcoes = {
					title: dadosEmit.titulo
				}
			}

			let req = await axios.post(url, opcoes)

			toastAtivo.value = true
			toastMessage.value = req.data.message
			closeColunaModal()
			busca()
		}

		const closeColunaModal = () => {
			modalColunaOpen.value = false;
		};

		// Aqui não precisou fazer requisição porque são poucos dados que podem ser trazidos dentro dos elementos
		const editStatus = (dadosEmit) => {
			if (dadosEmit.modoEdicao == false) {
				editandoStatus.value = false
				valorInicialTituloStatus.value = ''
				statusId.value = null
			} else {
				editandoStatus.value = true
				statusId.value = dadosEmit.statusId
				valorInicialTituloStatus.value = dadosEmit.title
			}
			abrirColunaModal()
		}

		const editTarefa = async (dadosEmit) => {
			// let taskId = dadosEmit.taskId
			taskId.value = dadosEmit.taskId
			let res = await axios.get(`http://localhost:8000/api/task/${taskId.value}`)
			valorInicialTituloTarefa.value = res.data.title
			valorInicialDescricaoTarefa.value = res.data.description
			valorInicialPrioridadeIdTarefa.value = res.data.priority_id
			valorInicialCorTarefa.value = res.data.background_color
			openTarefaModal(dadosEmit)
		}

		const excluirItem = async (dadosEmit) => {
			ConfirmExclusaoModalShow.value = true
			itemParaExcluir.value = dadosEmit.itemParaExcluir

			if (dadosEmit.itemParaExcluir == 'tarefa') {
				taskId.value = dadosEmit.id
			}

			if (dadosEmit.itemParaExcluir == 'status') {
				statusId.value = dadosEmit.id
			}
		}

		const confirmarExclusao = () => {
			if (itemParaExcluir.value == 'tarefa') {
				excluirTarefa()
			}

			if (itemParaExcluir.value == 'status') {
				excluirStatus()
			}

			ConfirmExclusaoModalShow.value = false
			itemParaExcluir.value = null
		}

		const excluirTarefa = async () => {
			const req = await axios.delete(`http://localhost:8000/api/task/${taskId.value}`)
			taskId.value = null
			toastAtivo.value = true
			toastMessage.value = req.data.message
			busca()
		}

		const excluirStatus = async () => {
			const req = await axios.delete(`http://localhost:8000/api/status/${statusId.value}`)
			statusId.value = null
			toastAtivo.value = true
			toastMessage.value = req.data.message
			busca()
		}


		const busca = async () => {
			let res = await axios.get('http://localhost:8000/api/statuses')
			statuses.value = res.data.statuses
			priorities.value = res.data.priorities
		}

		onMounted(() => {
			busca();
		});

		return {
			statuses,
			modalTarefaOpen,
			openTarefaModal,
			closeTarefaModal,
			salvarTarefa,
			toastAtivo,
			toastMessage,
			modalColunaOpen,
			abrirColunaModal,
			salvarStatus,
			closeColunaModal,
			editStatus,
			editTarefa,

			Tarefa2ModalOpen,
			valorInicialTituloStatus,
			statusId,
			priorities,

			valorInicialTituloTarefa,
			valorInicialDescricaoTarefa,
			valorInicialPrioridadeIdTarefa,
			valorInicialCorTarefa,
			excluirItem,
			ConfirmExclusaoModalShow,
			confirmarExclusao,
			itemParaExcluir,
			excluirTarefa,
			excluirStatus,
			editandoTarefa,
			editandoStatus
		};
	},
});
</script>

<style scoped>
.botao {
	
}

.botao:hover {
	transition: .5s;
	color: #e7953e;
}
</style>