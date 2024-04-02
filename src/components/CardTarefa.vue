<template>
    <div>
        <v-card
            class="card h-10 cursor-grab pa-2"
            :class="corClara(bgColor) ? 'text-dark' : 'text-light'"            
            :style="{
                backgroundColor: bgColor,
                borderTop: `3px solid ${tornarCorMaisIntensa(bgColor)}`,
                borderRight: `3px solid ${tornarCorMaisIntensa(bgColor)}`,
                borderLeft: `3px solid ${tornarCorMaisIntensa(bgColor)}`,
                borderBottom: `3px solid ${tornarCorMaisIntensa(bgColor)}`
            }"
        >
            <v-card class="elevation-0" color="transparent">
                <div class="d-flex justify-space-between">
                    <span>{{ taskTitle }} - ordem: {{ taskOrder }}</span>

                    <v-tooltip text="Opções" location="end">
                        <template v-slot:activator="{ props }">
                            <i v-bind="props" class="bi bi-three-dots opcoes" @click="opcoes"></i>
                        </template>
                    </v-tooltip>
                </div>
            </v-card>
        </v-card>
    </div>
</template>

<script>
import { defineComponent } from 'vue'
export default defineComponent ({
    name: 'CardTarefa',

    props: {
        bgColor: String,
        taskTitle: String,
        taskOrder: Number
    },

    setup () {
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


        const opcoes = () => {
            console.log('opções');
        }

        return {
            opcoes,
            corClara,
            tornarCorMaisIntensa
        }
    }
})
</script>

<style scoped>

.card {
	min-height: 60px;
	height: auto;
	max-width: 250px;
}
</style>