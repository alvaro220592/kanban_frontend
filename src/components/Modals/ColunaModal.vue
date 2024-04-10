<template>
    <v-dialog max-width="500px" persistent>
		<v-card color="grey-darken-4">
			<v-card-title>
				<span class="headline">Nova coluna</span>
			</v-card-title>
			<v-card-text>
				<v-row>
					<v-col cols="12" md="12">
						<v-text-field v-model="titulo" :counter="10" label="Título" hide-details required></v-text-field>
					</v-col>
				</v-row>
			</v-card-text>

			<v-card-actions class="justify-space-between">
				<v-btn color="blue darken-1" text @click="fecharColunaModal">Fechar</v-btn>
				<v-btn color="blue darken-1" text @click="salvarStatus">Salvar</v-btn>
			</v-card-actions>
		</v-card>
    </v-dialog>
</template>

<script>
import { defineComponent, ref, watch } from 'vue';

export default defineComponent({
    name: 'ColunaModal',

	props: {
		valorInicialTituloStatus: String
	},
	
    setup (props, { emit }) {

        // const titulo = ref('')
		const titulo = ref(props.valorInicialTituloStatus)
		const modoEdicao = ref(false)

		// Se tiver um valor no campo, algumas coisas são definidas
		watch(() => props.valorInicialTituloStatus, (novoValor) => {
			modoEdicao.value = true
			titulo.value = novoValor;
		});

        const salvarStatus = () => {
            emit('emitSalvarStatus', {
                titulo: titulo.value,
				modoEdicao: modoEdicao.value
            })
			fecharColunaModal()
        }

        const fecharColunaModal = () => {
			titulo.value = ''
			emit('emitFecharColunaModal')
		}

        return {
            titulo,
            salvarStatus,
            fecharColunaModal,
			modoEdicao
        }
    }
})
</script>

<style lang="scss" scoped>

</style>