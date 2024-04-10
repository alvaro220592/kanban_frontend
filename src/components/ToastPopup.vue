<template>
    <v-snackbar timeout="3000" color="grey-darken-3" elevation="24" location="top right">
        <div class="d-flex flex-column justify-space-between">
            <span :style="{fontSize: '20px', fontStyle: 'italic'}">
                {{ toastMessage }}
            </span>
            <v-progress-linear indeterminate absolute :color="cor"></v-progress-linear>
        </div>
    </v-snackbar>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent ({
    name: 'ToastPopup',
    
    props: {
        toastMessage: String,
        ativo: Boolean,
        cor: String
    }
})
</script>

<style lang="scss" scoped>

</style>