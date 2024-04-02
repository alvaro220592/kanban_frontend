<template>
    <v-snackbar timeout="3000" :color="cor" elevation="24" location="top right">
        <div class="d-flex flex-column justify-space-between">
            <span :style="{fontSize: '20px', fontStyle: 'italic'}">
                {{ toastMessage }}
            </span>
            <v-progress-linear indeterminate absolute color="deep-orange-darken-2"></v-progress-linear>
        </div>
    </v-snackbar>
</template>

<script>

export default {
    props: {
        toastMessage: String,
        ativo: Boolean,
        cor: String
    }
}
</script>

<style lang="scss" scoped>

</style>