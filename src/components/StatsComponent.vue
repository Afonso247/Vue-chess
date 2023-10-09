<template>
    <div class="stats-container">
        <div class="content-container">
            <img src="/images/dishes.png" alt="dish-img">
            <div class="text-container">
                <h1>Dish Dash</h1>
                <ul>
                    <li>O garçom selecionado deixará um prato sujo em uma das pilhas em 
                        pequenos intervalos - <span>
                        Lave todos pratos rapidamente para vencer o jogo
                    </span></li>
                    <li>A quantidade máxima tolerável de pratos não lavados é 5 - <b>
                        se este valor for ultrapassado, você perde
                    </b></li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Stats"
    }
</script>

<style scoped>
    .stats-container {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 50vh;
    }
    .content-container {
        margin-left: 20px;
        max-width: 300px;
        text-align: flex-start;
    }
    .content-container img {
        max-width: 250px;
    }
    .text-container li {
        color: #222;
        max-width: 100%;
        font-size: 14px;
        word-wrap: break-word;
        list-style: none;
        margin-left: -1rem;
        border-bottom: 1px solid #000;
        margin-bottom: 10px;
    }
    .text-container li b {
        color: #ff0000;
    }
    .text-container li span {
        color: #0047AB;
    }
    .stats-container h1 {
        color: #0047AB;
        text-align: center;
        font-size: 1.45rem;
        margin-bottom: 20px;
        text-transform: uppercase;
    }
</style>