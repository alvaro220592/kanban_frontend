<template>
    <v-app>
        <v-card class="board pa-4 h-screen d-flex flex-row overflow-x-auto gap-2 w-100 rounded-0" color="grey-darken-4">
            <v-card v-for="status in statuses" :key="status.id" class="coluna pa-4" color="grey-darken-3">
                <h4 class="p-2 d-flex justify-content-between">
                    {{ status.title }}
                    <i class="bi bi-plus-circle" @mouseover="iconOver" @mouseleave="iconLeave" style="cursor: pointer" @click="openModal"></i>
                </h4>

                <VueDraggable v-model="status.tasks" class="cardContainer d-flex flex-column gap-2" group="statuses"
                    animation="250" ghostClass="ghost" :data-status_id="status.id" @start="drag" @dragover="handleDragOver" @dragleave="handleDragLeave" @end="drop">
                    
                    <v-card v-for="task in status.tasks" :key="task.id" class="card pa-2"
                        :class="corClara(task.background_color) ? 'text-dark' : 'text-light'" :data-task_id="task.id"
                        :data-order="task.order" :style="{ backgroundColor: task.background_color }">
                        <v-card class="d-flex justify-content-between elevation-0" color="transparent">
                            <span>{{ task.title }} - ordem: {{ task.order }}</span>
                            <i class="bi bi-three-dots opcoes" @click="opcoes"></i>
                        </v-card>
                    </v-card>
                </VueDraggable>
            </v-card>
            <v-card id="a"></v-card>
        </v-card>
    </v-app>

	<!-- modal -->
    <v-dialog v-model="modalOpen" max-width="500px">
		<v-card color="grey-darken-3">
			<v-card-title>
				<span class="headline">Nova tarefa</span>
			</v-card-title>
			<v-card-text>

				<v-row>
					<v-col
						cols="12"
						md="4"
					>
						<v-text-field
							v-model="taskTitle"
							:counter="10"
							label="Título"
							hide-details
							required
						></v-text-field>
					</v-col>

					<v-col class="cols-12 md-4">
						<v-textarea label="Descrição" v-model="taskDescription"></v-textarea>
					</v-col>
				</v-row>

				<v-btn color="blue darken-1" @click="openCoresModal">Selecionar cor</v-btn>
			</v-card-text>

			<v-card-actions class="justify-space-between">
				<v-btn color="blue darken-1" text @click="closeModal">Fechar</v-btn>
				<v-btn color="blue darken-1" text @click="closeModal">Salvar</v-btn>
			</v-card-actions>
		</v-card>
    </v-dialog>

	<!-- modal cores -->
	<v-dialog v-model="modalCoresOpen" max-width="250px">
		<v-card color="grey-darken-3">
			<v-card-text>
				<div v-for="(cor, index_cores) in cores" :key="index_cores">
					<div class="d-flex justify-center align-center">
						<div class=" rounded-circle" v-for="(hexa, index_hexas) in cor.hexas" :key="index_hexas">
							<v-btn
								icon="mdi-circle"
								variant="text"
								:style="{color: hexa}"
								@click="closeCoresModal"
							></v-btn>
						</div>
					</div>
				</div>
			</v-card-text>
		</v-card>
    </v-dialog>
    
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import { VueDraggable } from 'vue-draggable-plus';

import axios from 'axios'
export default defineComponent({
	name: 'ConteudoGeral',
	components: {
		VueDraggable,
	},
	setup() {
		const statuses = ref([])
		const taskShow = ref()
		const modalOpen = ref(false);
		const modalCoresOpen = ref(false);
		const taskTitle = ref('')
		const taskDescription = ref('')

		const cores = [
			{
				nome: 'vermelho',
				hexas: ['#EF9A9A','#E57373','#EF5350','#F44336']
			},
			{
				nome: 'roxo',
				hexas: ['#CE93D8', '#BA68C8', '#AB47BC', '#9C27B0']
			},
			{
				nome: 'azul',
				hexas: ['#90CAF9', '#64B5F6', '#42A5F5', '#2196F3']
			},
			{
				nome: 'verde',
				hexas: ['#A5D6A7', '#81C784', '#66BB6A', '#4CAF50']
			},
			{
				nome: 'amarelo',
				hexas: ['#FFF176', '#FFEE58', '#FFEB3B', '#FDD835']
			},
			{
				nome: 'laranja',
				hexas: ['#FFCC80', '#FFB74D', '#FFA726', '#FF9800']
			}			
		];

		const openModal = () => {
			modalOpen.value = true;
		};

		const closeModal = () => {
			modalOpen.value = false;
		};

		const openCoresModal = () => {
			modalCoresOpen.value = true;
		};

		const closeCoresModal = () => {
			modalCoresOpen.value = false;
		};

		const iconOver = (event) => {
			event.target.classList.remove('bi-plus-circle')
			event.target.classList.add('bi-plus-circle-fill')
		}

		const iconLeave = (event) => {
			event.target.classList.remove('bi-plus-circle-fill')
			event.target.classList.add('bi-plus-circle')
		}

		const drag = () => {
			// console.log(event.item.dataset.task_id)
		}

		const handleDragOver = (event) => {
			let cardContainer = event.target.closest('.cardContainer')

			if (cardContainer) {
				cardContainer.classList.add('cardContainerBorder')
			}
		}

		const handleDragLeave = () => {
			removerBordas()
		}

		const removerBordas = () => {
			let cardContainers = document.querySelectorAll('.cardContainer')
			cardContainers.forEach(item => {
				item.classList.remove('cardContainerBorder')
			})
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

			busca()
		}

		const busca = async () => {
			let res = await axios.get('http://localhost:8000/api/statuses')
			statuses.value = res.data
		}

		// Função que usa a fórmula "YIQ" que serve pra detectar se a cor é clara ou escura
		const corClara = (hexColor) => {
			// Remover o '#' do valor hexadecimal
			hexColor = hexColor.replace('#', '');

			// Converter o valor hexadecimal para RGB
			const r = parseInt(hexColor.substr(0, 2), 16);
			const g = parseInt(hexColor.substr(2, 2), 16);
			const b = parseInt(hexColor.substr(4, 2), 16);

			// Calcular a luminosidade relativa (Y) usando a fórmula YIQ
			const yiq = ((r * 299) + (g * 587) + (b * 114)) / 1000;

			// Definir um limiar para decidir se a cor é clara ou escura
			const threshold = 128;

			// Comparar a luminosidade relativa com o limiar e retornar verdadeiro se for maior (cor clara)
			return yiq >= threshold;
		}

		// abre um modal da tarefa
		const opcoes = async (event) => {
			let card = event.target.closest('.card');
			let task_id = card.dataset.task_id
			let dados = await axios.get(`http://localhost:8000/api/task/${task_id}`)
			taskShow.value = dados.data
			document.getElementById('a').innerText = taskShow.value.description
		}


		onMounted(() => {
			busca();
		});

		return {
			drag,
			drop,
			statuses,
			corClara,
			opcoes,
			handleDragOver,
			handleDragLeave,
			iconOver,
			iconLeave,
			modalOpen,
			openModal,
			closeModal,
			cores,
			modalCoresOpen,
			openCoresModal,
			closeCoresModal,
			taskTitle,
			taskDescription
		};
	},
});
</script>

<style scoped>

.board {
	width: 98%;
	display: flex;
	flex-direction: row;
	gap: 10px;
	overflow-x: auto;
	height: 95vh;
	padding: 10px;
}

.coluna {
	background-color: #2f2f2f;
	padding: 0px 10px;
	min-width: 250px;
	max-width: 250px;
	border-radius: 4px;
	box-shadow: 1px 1px 5px black;
	color: white;
	overflow-y: auto;
	height: 90vh;
}

.cardContainer {
	display: flex;
	flex-direction: column;
	gap: 8px;
}

.cardContainerBorder {
	border: 3px dashed #9b95a5;
	background-color: transparent;
	padding: 2px 2px;
}

.card {
	background-color: #f4f4f4;
	/* border: 1px solid #ddd; */
	padding: 10px;
	border-radius: 4px;
	cursor: grab;
	min-height: 40px;
	height: auto;
	max-width: 250px;
}

.ghost {
	opacity: 0;
	background-color: #f4f4f4;
}

.opcoes {
	cursor: pointer;
}

</style>