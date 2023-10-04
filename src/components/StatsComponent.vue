<template>
    <div class="hangman-container">
        <img src="/images/dishes.png" alt="dish-img">
        <h1>Dish Dash</h1>
    </div>
</template>

<script>
    export default {
        name: "Stats"
    }
</script>

<style scoped>
    .hangman-container img {
        max-width: 180px;
    }
    .hangman-container h1 {
        color: #000;
        text-align: center;
        font-size: 1.45rem;
        margin-top: 20px;
        text-transform: uppercase;
    }
</style>