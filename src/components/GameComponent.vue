<template>
    <div class="game-container">
        <div class="msg" v-if="showMsg">
            <p>{{ msg }}</p>
        </div>
        <div class="empilhar-pratos" v-for="(acumulo, index) in acumuloPratos" :key="index">
            <h2>Pilha {{ acumulo.id }}</h2>
            <div class="prato-container">
                <div
                v-for="(prato, pratoIndex) in acumulo.pratos"
                :key="pratoIndex"
                class="prato"
                >
                {{ prato }}
                </div>
            </div>
            <!-- <button 
                class="prato-btn" 
                @click="lavarPrato(index)" 
                :disabled="estaLavando"
                >
                Lavar Prato
            </button> -->
        </div>
        <div class="start-btn">
            <button @click="startJogo" v-show="onStandby">Iniciar</button>
        </div>
        <div v-show="!onStandby" class="prato-type">
            <input type="text" placeholder="Digite o código de um prato..." v-model="inputValue" @input="lavarPrato(inputValue)">
            <p>{{ mensagemLavagem }}</p>
        </div>
    </div>
</template>

<script>
import Teclado from './TecladoComponent.vue';

    export default {
    name: "Game",
    components: { Teclado },
    data() {
        return {
            pilhaAtual: 0,
            inputValue: '',
            mensagemLavagem: '',
            onStandby: true,
            showMsg: false,
            msg: 'bruh',
            acumuloPratos: [
                { id: 1, pratos: ['40EAB6'] },
                { id: 2, pratos: [] },
                { id: 3, pratos: [] },
                { id: 4, pratos: [] },
                { id: 5, pratos: [] }
            ]
        }
    },
    methods: {
        getRandomHex() {
            // Gera um código Hex aleatório
            const letters = '0123456789ABCDEF';
            let hex = '';

            for (let i = 0; i < 6; i++) {
                hex += letters[Math.floor(Math.random() * 16)];
            }

            return hex;
        },
        lavarPrato(valorInput) {

            // Verifique se o valor inserido corresponde a algum prato nas pilhas
            const pratoLavado = this.acumuloPratos
                .flatMap((acumulo) => acumulo.pratos)
                .find((prato) => prato === valorInput);

            if (pratoLavado) {
                // Remova o prato da pilha
                const stack = this.acumuloPratos.find((acumulo) =>
                acumulo.pratos.includes(pratoLavado)
                );
                stack.pratos = stack.pratos.filter((prato) => prato !== pratoLavado);
                this.inputValue = ''

                // Simule a lavagem do prato (você pode adicionar sua lógica real aqui)
                this.showMsg = true;

                // Defina a mensagem de sucesso
                this.msg = `Prato ${pratoLavado} lavado com sucesso!`;
            } else {
                // Se não houver correspondência, exiba uma mensagem de erro
                this.msg = 'Não foi possível lavar o prato. Código inválido.';
            }
        },
        startJogo() {
            const interval = setInterval(() => {

                this.onStandby = false
                const totalPratos = this.acumuloPratos.reduce((total, acumulo) => total + acumulo.pratos.length, 0);
                // Verifica se todas as pilhas estão vazias
                const todasPilhasVazias = this.acumuloPratos.every(acumulo => acumulo.pratos.length === 0);

                if (totalPratos >= 12) {
                    clearInterval(interval); // Encerra o loop
                    this.showMsg = true;
                    this.msg = 'Jogo encerrado, 12 ou mais pratos acumulados.';
                    return this.onStandby = true;
                }

                if (todasPilhasVazias) {
                    clearInterval(interval); // Encerra o loop
                    this.showMsg = true;
                    this.msg = 'Você venceu! Todas as pilhas estão vazias.';
                    return this.onStandby = true;
                }

                // Adiciona aleatoriamente entre as pilhas
                const randomIndex = Math.floor(Math.random() * this.acumuloPratos.length);

                const pilhaSelecionada = this.acumuloPratos[randomIndex];
                if (pilhaSelecionada.pratos.length < 4) {
                    const hex = this.getRandomHex()
                    pilhaSelecionada.pratos.push(hex);
                }


                // Adiciona até a pilha estiver cheia
                // const pilhaSelecionada = this.acumuloPratos[this.pilhaAtual];
                // if (pilhaSelecionada.pratos.length < 4) {
                //     const hex = this.getRandomHex()
                //     pilhaSelecionada.pratos.push(hex);
                // } else {
                //     this.pilhaAtual = (this.pilhaAtual + 1) % this.acumuloPratos.length;
                // }

            }, 5000); // Intervalo de 3 segundos
        },
    }
}
</script>

<style scoped>
    .empilhar-pratos {
        display: flex;
        gap: 10px;
        list-style: none;
        align-items: center;
        justify-content: center;
        color: #222;
    }
    .empilhar-pratos .prato-container {
        display: flex;
        flex-direction: row;
        margin-bottom: 10px;
    }
    .msg {
        color: #0056b3;
        text-align: center;
        margin-bottom: 20px;
    }

    .prato {
        border: 1px solid #ccc;
        padding: 5px 10px;
        margin-right: 10px;
    }
    .prato-btn {
        margin-left: auto;
    }

    .start-btn {
        display: flex;
        align-items: center;
        justify-content: center;
        margin-top: 20px;
    }
    .start-btn button {
        background-color: #0074d9;
        color: #fff;
        padding: 15px 20px;
        font-size: 18px;
        font-weight: bold;
        border-radius: 8px;
        transition: background-color 0.3s ease-in-out;
    }
    .start-btn button:hover {
        background-color: #0056b3;
    }

    .prato-type {
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .prato-type input {
        padding: 10px;
        width: 100%;
        border: 1px solid #000;
        border-radius: 5px;
        font-size: 16px;
        outline: none;
    }
    .prato-type input:focus {
        border-color: #007bff; /* Cor da borda quando focado */
        box-shadow: 0 0 5px rgba(0, 123, 255, 0.5); /* Sombra quando focado */
    }
</style>