<template>
    <div>
        <v-card
            class="card h-10 cursor-grab pa-2"
            color="grey-darken-3"
            :style="{
                backgroundColor: 'grey',
                borderTop: `4px solid ${tornarCorMaisIntensa(bgColor)}`
            }"
        >
            <v-card class="elevation-0" color="transparent">
                <div class="d-flex justify-space-between mb-1">
                    
                    <span class="font-weight-bold text-grey-lighten-2">{{ taskTitle }}</span>

                    <v-menu open-on-click transition="slide-y-transition" color="grey-darken-3">
                        <template v-slot:activator="{ props }">
                            <p>
                                <i class="bi bi-list opcoes" v-bind="props" style="cursor: pointer"></i>
                            </p>
                        </template>

                        <v-card color="grey-darken-3" class="d-flex flex-column" :style="{border: '1px solid #263238'}">
                            <span class="menuStatusSpan" :data-status_id="statusId" @click="editTarefa">Editar</span>
                            <span class="menuStatusSpan" @click="excluirTarefa">Excluir</span>
                        </v-card>
                    </v-menu>
                    
                </div>

                <div class="d-flex text-grey-lighten-1 my-1py-1 px-2" style="font-size: 14px;">
                    <span>{{ formatarData(taskCreatedAt) }}</span>
                </div>

                <div class="d-flex justify-space-between text-grey-lighten-1 my-1 py-1 px-2 rounded-1 cursor-pointer descricao_tarefa" @click="mudarExibicaoDescricao">
                    <span>{{ exibirDescricao(taskDescription) }}</span>
                    <i v-show="exibirDescricao(taskDescription).length > limiteExibicaoDescricao" class="bi bi-chevron-down icone_descricao"></i>
                </div>

                <!-- <v-icon icon="mdi-circle" class="icone_prioridade" :style="{color: '#4CAF50'}"></v-icon> -->
                <v-tooltip :text="'Prioridade: ' + taskPriorityName">
                    <template v-slot:activator="{ props }">
                        <v-icon v-bind="props" icon="mdi-circle" class="icone_prioridade" :style="{color: setTaskPriorityColor(taskPriorityName)}"></v-icon>
                    </template>
                </v-tooltip>
            </v-card>
        </v-card>
    </div>
</template>

<script>
import { defineComponent, ref } from 'vue'
import axios from 'axios'
import {format} from 'date-fns'

export default defineComponent ({
    name: 'CardTarefa',

    props: {
        bgColor: String,
        taskId: Number,
        taskTitle: String,
        taskOrder: Number,
        taskDescription: String,
        taskCreatedAt: String,
        taskPriorityName: String,
        taskPriorityId: Number
    },

    setup (props, {emit}) {
        const taskShow = ref();
        const descricaoExibida = ref('')
        const refLimitarTexto = ref(true)
        const limiteExibicaoDescricao = ref(20)
        const taskPriorityColor = ref('')

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

        const tornarCorMaisIntensa = (cor) => {
			let fator = 1.5
			// Extrair os componentes R, G e B da cor
			let r = parseInt(cor.substring(1, 3), 16);
			let g = parseInt(cor.substring(3, 5), 16);
			let b = parseInt(cor.substring(5, 7), 16);

			// Aplicar o fator para tornar a cor mais intensa
			r = Math.min(Math.floor(r / fator), 255);
			g = Math.min(Math.floor(g / fator), 255);
			b = Math.min(Math.floor(b / fator), 255);

			// Converter os valores R, G e B de volta para hexadecimal
			r = r.toString(16).padStart(2, '0');
			g = g.toString(16).padStart(2, '0');
			b = b.toString(16).padStart(2, '0');

			// Retornar a nova cor em formato hexadecimal
			return `#${r}${g}${b}`;
		}

        // abre um modal da tarefa
		const opcoes = async (event) => {
            let card = event.target.closest('.card');
			let task_id = card.dataset.task_id
			let dados = await axios.get(`http://localhost:8000/api/task/${task_id}`)
			taskShow.value = dados.data
            // document.getElementById('a').innerText = taskShow.value.description
		}

        const limitarTexto = (texto) => {
            let textoLimitado = texto.slice(0, limiteExibicaoDescricao.value)
            if (texto <= textoLimitado) {
                return textoLimitado
            }
            return `${textoLimitado}...`
        }

        const exibirDetalhesTarefa = () => {
            console.log('detalhes');
        }

        const exibirDescricao = (texto) => {
            if (refLimitarTexto.value == true) {
                return limitarTexto(texto)
            } else {
                return texto
            }
        }

        const mudarExibicaoDescricao = (event) => {
            refLimitarTexto.value = !refLimitarTexto.value
            let icone_descricao = event.currentTarget.querySelector('.icone_descricao');

            // Se a exibição do texto não estiver limitada, aparece o ícone de setinha pra cima pra pessoa ocultar a exibição se quiser
            if (refLimitarTexto.value == false) {
                icone_descricao.classList.remove('bi-chevron-down')
                icone_descricao.classList.add('bi-chevron-up')
            } else {
                icone_descricao.classList.remove('bi-chevron-up')
                icone_descricao.classList.add('bi-chevron-down')
            }
        }

        const formatarData = (data) => {
            return format(new Date(data), 'dd/MM/yyyy HH:mm')
        }

        const setTaskPriorityColor = (taskPriorityName) => {
            let color = ''

            if (taskPriorityName.toLowerCase() == 'baixa') {
                color = '#7dd75b' // verde
            } else

            if (taskPriorityName.toLowerCase() == 'média') {
                color = '#f3c918' // laranja
            } else

            if (taskPriorityName.toLowerCase() == 'alta') {
                color = '#e54141' // vermelho
            }

            return color
        }

        const editTarefa = () => {
            emit('emitEditTarefa',{
                taskId: props.taskId,
                modoEdicao: true
            })
        }

        const excluirTarefa = () => {
            emit('emitExcluirTarefa', {
                itemParaExcluir: 'tarefa',
                id: props.taskId
            })
        }

        return {
            opcoes,
            corClara,
            tornarCorMaisIntensa,
            taskShow,
            limitarTexto,
            exibirDetalhesTarefa,
            descricaoExibida,
            exibirDescricao,
            refLimitarTexto,
            mudarExibicaoDescricao,
            limiteExibicaoDescricao,
            formatarData,
            taskPriorityColor,
            setTaskPriorityColor,
            editTarefa,
            excluirTarefa
        }
    }
})
</script>

<style>

.card {
	min-height: 60px;
	height: auto;
	max-width: 300px;
}

.opcoes {
	cursor: pointer;
    padding: 0px 2px;
    border-radius: 2px;
    transition: .3s;
}

.opcoes:hover {
    background-color: rgba(86, 86, 86, 0.367);
    transition: .3s;
}

.descricao_tarefa {
    font-size: 14px;
}

.descricao_tarefa:hover {
    /* border: 1px solid #626262; */
    background-color: #3a3a3a;
}

.icone_prioridade {
    font-size: 15px;
    cursor: default;
}
</style>