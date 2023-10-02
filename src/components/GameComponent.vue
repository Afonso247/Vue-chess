<template>
    <div class="game-container">
        <ul class="mostrar-palavra">
            <li class="letra"></li>
            <li class="letra adivinhada">g</li>
            <li class="letra"></li>
            <li class="letra"></li>
            <li class="letra"></li>
            <li class="letra"></li>
            <li class="letra"></li>
        </ul>
        <h4 class="texto-dica">
            Dica: 
            <b>Lorem ipsum dolor sit amet consectetur adipisicing elit. Quos, quae?</b>
        </h4>
        <h4 class="texto-tentativas">
            Tentativas restantes: 
            <b>0 / 6</b>
        </h4>
        <Teclado />
    </div>
</template>

<script>
import Teclado from './TecladoComponent.vue';

    export default {
    name: "Game",
    components: { Teclado }
}
</script>

<style scoped>
    .mostrar-palavra {
        display: flex;
        gap: 10px;
        list-style: none;
        align-items: center;
        justify-content: center;
    }
    .mostrar-palavra .letra {
        margin-bottom: 30px;
        width: 28px;
        font-size: 2rem;
        font-weight: 600;
        text-transform: uppercase;
        text-align: center;
        border-bottom: 3px solid #000;
    }
    .mostrar-palavra .letra.adivinhada {
        border-color: transparent;
        margin: -30px 0 35px;
    }
    .game-container h4 {
        color: #000;
        text-align: center;
        font-size: 1.1rem;
        font-weight: 500;
        margin-bottom: 15px;
    }
    .game-container .texto-dica b {
        font-weight: 600;
    }
    .game-container .texto-tentativas b {
        color: #ff0000;
    }
</style>