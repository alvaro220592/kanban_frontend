<template>
    <v-dialog max-width="300" persistent>
        <!-- <template v-slot:default="{ isActive }"> -->
            <v-card title="Deseja Excluir?" color="grey-darken-4">
                <v-card-text>
                    
                </v-card-text>

				<v-card-actions class="justify-space-between">
					<v-btn color="blue darken-1" text >Cancelar</v-btn>
					<v-btn color="blue darken-1" text @click="confirmarExclusao">Excluir</v-btn>
				</v-card-actions>
            </v-card>
        <!-- </template> -->
    </v-dialog>
</template>

<script>
import { defineComponent } from 'vue'

export default defineComponent({
    props: {
        funcaoChamada: Function
    },
    name: 'ConfirmExclusaoModal',

    setup(props) {

        const confirmarExclusao = () => {
            props.funcaoChamada()
        }

        return {
            confirmarExclusao
        }
    }
})
</script>

<style lang="scss" scoped></style>