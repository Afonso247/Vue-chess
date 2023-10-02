<template>
    <div class="hangman-container">
        <img src="/images/hangman-0.svg" alt="hangman-img">
        <h1>Jogo da Forca</h1>
    </div>
</template>

<script>
    export default {
        name: "Hangman"
    }
</script>

<style scoped>
    .hangman-container img {
        max-width: 270px;
    }
    .hangman-container h1 {
        color: #000;
        text-align: center;
        font-size: 1.45rem;
        margin-top: 20px;
        text-transform: uppercase;
    }
</style>