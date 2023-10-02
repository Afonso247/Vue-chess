<template>
    <div class="teclado">
        <div v-for="row in tecladoRows" :key="row">
            <div v-for="key in row" :key="key" @click="onKeyPress(key)" class="teclado-key">
                {{ key }}
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'Teclado',
        data() {
            return {
            tecladoRows: [
                ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'],
                ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L', 'Ç'],
                ['Z', 'X', 'C', 'V', 'B', 'N', 'M'],
            ],
            };
        },
        methods: {
            onKeyPress(key) {
            // Aqui você pode fazer algo com a tecla pressionada, como emitir um evento ou executar uma ação.
            console.log('Tecla pressionada:', key);
            },
        }
    }
</script>

<style scoped>
    .teclado {
        margin-top: 30px;
    }
    .teclado .teclado-key {
        color: #000;
        display: inline-block;
        padding: 10px;
        margin: 5px;
        border: 1px solid #868585;
        cursor: pointer;
    }
    .teclado .teclado-key:hover {
        background-color: #dbdbdb;
    }
</style>