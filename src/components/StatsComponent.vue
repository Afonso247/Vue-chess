<template>
    <div class="stats-container">
        <div class="content-container">
            <img src="/images/chess.png" alt="chess-img">
            <div class="text-container">
                <h1>Chess</h1>
                <ul>
                    <li>Uma joguinho tradicional de xadrez - <span>
                        jogue para prender o rei adversário e aplicar xeque-mate 
                        em seu oponente
                    </span></li>
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