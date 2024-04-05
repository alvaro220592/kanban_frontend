<template>
    <v-dialog max-width="500" persistent>
        <!-- <template v-slot:default="{ isActive }"> -->
            <v-card title="Dialog" color="grey-darken-4">
                <v-card-text>
                    <v-row>
						<v-col cols="12" md="12">
							<v-text-field v-model="titulo" :counter="10" label="Título" hide-details required></v-text-field>
						</v-col>

						<v-col class="cols-12 md-12">
							<v-textarea label="Descrição" v-model="descricao"></v-textarea>
						</v-col>
					</v-row>

                    <!-- DROPDOWN de seleção de cores -->
					<div>
						<v-menu open-on-click transition="slide-y-transition">
							<template v-slot:activator="{ props }">
								<v-btn color="primary" v-bind="props">Cor da tarefa</v-btn>
							</template>

							<!-- Card com as cores disponíveis -->
							<v-card class="py-2 px-6" color="grey-darken-3">
								<div class="text-center text-button">Selecione</div>
								<div v-for="(cor, index_cores) in cores" :key="index_cores">
									<div class="d-flex justify-center align-center">
										<div class="rounded-circle" v-for="(hexa, index_hexas) in cor.hexas" :key="index_hexas">
											<v-btn
												icon="mdi-circle"
												variant="text"
												:style="{color: hexa}"
												:data-cor_hexadecimal="hexa"
												@click="selecionarCorTarefa(hexa)"
											></v-btn>
										</div>
									</div>
								</div>
							</v-card>
						</v-menu>

						<v-icon v-if="corTarefaSelecionada != ''" icon="mdi-circle" :style="{color: corTarefaSelecionada}" end></v-icon>
						<span v-else class="ml-3">Nenhuma selecionada</span>
					</div>
                </v-card-text>

                <!-- <v-card-actions>
                    <v-spacer></v-spacer>

                    <v-btn text="Close Dialog" @click="isActive.value = false"></v-btn>
                </v-card-actions> -->


				<v-card-actions class="justify-space-between">
					<v-btn color="blue darken-1" text @click="fecharTarefaModal">Fechar</v-btn>
					<v-btn color="blue darken-1" text @click="salvarTarefa">Salvar</v-btn>
				</v-card-actions>
            </v-card>
        <!-- </template> -->
    </v-dialog>
</template>

<script>
import { ref } from 'vue'

export default {
    setup (props, { emit }) {
		const titulo = ref('')
		const descricao = ref('')
		const corTarefaSelecionada = ref('')

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

		const salvarTarefa = () => {
			emit('emitSalvarTarefa', {
				titulo: titulo.value,
				descricao: descricao.value,
				corSelecionada: corTarefaSelecionada.value
			})
			fecharTarefaModal()
		}

		const fecharTarefaModal = () => {
			titulo.value = ''
			descricao.value = ''
			corTarefaSelecionada.value = ''
			emit('emitFecharTarefaModal')
		}

		const selecionarCorTarefa = (hexa) => {
			corTarefaSelecionada.value = hexa;
		}

        return {
			cores,
			titulo,
			descricao,
			salvarTarefa,
			fecharTarefaModal,
			selecionarCorTarefa,
			corTarefaSelecionada
		}
    }
}
</script>

<style lang="scss" scoped></style>