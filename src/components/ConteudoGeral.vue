<template>
	<v-app>
		<v-card class="d-block h-screen rounded-0" color="blue-darken-4">
			<v-container>
				<h1>Kanban</h1>
				<KanbanBoard>
					<StatusColuna v-for="status in statuses" :key="status.id">

						<TituloStatus :statusId="status.id" :statusTitle="status.title"
							@emitAbrirModalTarefa="openTarefaModal" @emitEditStatus="editStatus"></TituloStatus>

						<div class="overflow-y-auto px-2">
							<CardContainer :data-status_id="status.id" :busca="busca" v-model="status.tasks">
								<CardTarefa v-for="task in status.tasks" :key="task.id" :data-task_id="task.id"
									:taskTitle="task.title" :taskOrder="task.order" :bgColor="task.background_color">
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

	<!-- modal -->
	<!-- <TarefaModal
		v-model="modalTarefaOpen"
		@emitTarefaSalva="salvarTarefa"
		@emitFecharTarefaModal="closeTarefaModal"
	></TarefaModal> -->

	<Tarefa2Modal v-model="Tarefa2ModalOpen" @emitFecharTarefaModal="closeTarefaModal" @emitSalvarTarefa="salvarTarefa">
	</Tarefa2Modal>

	<ColunaModal v-model="modalColunaOpen" @emitSalvarStatus="salvarStatus" @emitFecharColunaModal="closeColunaModal"
		:valorInicialTitulo="valorInicialTitulo">
	</ColunaModal>

	<!-- toastAtivo -->
	<ToastPopup :toastMessage="toastMessage" cor="green" v-model="toastAtivo"></ToastPopup>
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

		Tarefa2Modal
	},
	setup() {
		const statuses = ref([])
		const modalTarefaOpen = ref(false);
		const novaTarefaStatusId = ref(null)
		const toastAtivo = ref(false)
		const toastMessage = ref('')
		const modalColunaOpen = ref(false);
		const valorInicialTitulo = ref('');
		const Tarefa2ModalOpen = ref(false)
		const statusId = ref(null)

		// const openTarefaModal = (event) => {
		// 	novaTarefaStatusId.value = event.target.dataset.status_id
		// 	modalTarefaOpen.value = true;
		// };

		const openTarefaModal = (dadosEmit) => {
			novaTarefaStatusId.value = dadosEmit.statusId
			Tarefa2ModalOpen.value = true
		};

		const salvarTarefa = async (dadosEmit) => {
			let status_id = novaTarefaStatusId.value

			let req = await axios.post('http://localhost:8000/api/task', {
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
			Tarefa2ModalOpen.value = false;
		};

		const abrirColunaModal = () => {
			modalColunaOpen.value = true
		}

		const salvarStatus = async (dadosEmit) => {
			
			let url = 'http://localhost:8000/api/status/store'
			let opcoes = {
				title: dadosEmit.titulo
			}

			if (dadosEmit.modoEdicao == true) {
				url = `http://localhost:8000/api/status/update/${statusId.value}`
				opcoes = {
					title: dadosEmit.titulo
				}
			}

			let req = await axios.post(url, opcoes)

			console.log(req.data);

			toastAtivo.value = true
			toastMessage.value = req.data.message
			closeColunaModal()
			busca()
		}

		const closeColunaModal = () => {
			modalColunaOpen.value = false;
		};

		const editStatus = (dadosEmit) => {
			console.log(dadosEmit)
			valorInicialTitulo.value = dadosEmit.title
			statusId.value = dadosEmit.statusId
			abrirColunaModal()
		}

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

			Tarefa2ModalOpen,
			valorInicialTitulo,
			statusId
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