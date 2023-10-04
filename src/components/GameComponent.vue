<template>
    <div class="game-container">
        <div class="msg" v-if="showMsg">
            <p>{{ msg }}</p>
        </div>
        <div class="empilhar-pratos" v-for="(acumulo, index) in acumuloPratos" :key="index">
            <div class="pilha-titulo">
                <h2>Pilha {{ acumulo.id }}</h2>
            </div>
            <div class="prato-container">
                <div
                v-for="(prato, pratoIndex) in acumulo.pratos"
                :key="pratoIndex"
                class="prato"
                >
                {{ prato }}
                </div>
            </div>
        </div>
        <div class="start-btn">
            <button @click="startJogo" v-show="onStandby">Iniciar</button>
        </div>
        <div v-show="!onStandby" class="prato-type">
            <input type="text" placeholder="Digite o código de um prato..." v-model="inputValue" @input="lavarPrato(inputValue)">
            <p>{{ mensagemLavagem }}</p>
        </div>
        <div class="leave-btn">
            <button @click="console.log('Clicou')">Sair do Jogo</button>
        </div>
    </div>
</template>

<script>

    export default {
    name: "Game",
    data() {
        return {
            pilhaAtual: 0,
            pratosLavados: 0,
            tempoGasto: 0,
            tempoMedio: 0,
            tempoInicial: null,
            inputValue: '',
            mensagemLavagem: '',
            onStandby: true,
            showMsg: false,
            msg: '',
            acumuloPratos: [
                { id: 1, pratos: [] },
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

            if(valorInput.length >= 6) {
                // Verifique se o valor inserido corresponde a algum prato nas pilhas
                const pratoLavado = this.acumuloPratos
                    .flatMap((acumulo) => acumulo.pratos)
                    .find((prato) => prato === valorInput.toUpperCase());

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

                    // Atualize a quantidade de pratos lavados
                    this.pratosLavados++;

                    // Calcule o tempo gasto
                    const tempoAtual = new Date();
                    const tempoDecorrido = (tempoAtual - this.tempoInicial) / 1000; // Em segundos
                    this.tempoGasto = tempoDecorrido.toFixed(2);
                } else {
                    // Se não houver correspondência, exiba uma mensagem de erro
                    this.msg = 'Não foi possível lavar o prato. Código inválido.';
                }
            } else {
                return
            }
        },
        startJogo() {
            this.tempoInicial = new Date();
            this.showMsg = true;
            this.msg = 'Digite o código de um prato para lava-lo'
            this.onStandby = false;

            const self = this
            
            const inserirPratos = (iteracoes, atraso) => {
                let contador = 0;

                function prox() {
                    if (contador < iteracoes) {
                        const randomIndex = Math.floor(Math.random() * self.acumuloPratos.length);

                        const pilhaSelecionada = self.acumuloPratos[randomIndex];
                        const hex = self.getRandomHex()
                        pilhaSelecionada.pratos.push(hex);
                    
                        contador++;

                        setTimeout(prox, atraso);
                    }
                }

                // Inicie a primeira iteração
                prox();
            }

            setTimeout(() => inserirPratos(2, 1000), 1000) // Adiciona aleatoriamente dois pratos entre as pilhas, com 1seg de intervalo

            const interval = setInterval(() => {

                const totalPratos = this.acumuloPratos.reduce((total, acumulo) => total + acumulo.pratos.length, 0);
                // Verifica se todas as pilhas estão vazias
                const todasPilhasVazias = this.acumuloPratos.every(acumulo => acumulo.pratos.length === 0);

                if (totalPratos >= 5) {
                    clearInterval(interval); // Encerra o loop
                    this.tempoGasto -= 5
                    if(this.pratosLavados > 0) {
                        this.tempoMedio = (this.tempoGasto / this.pratosLavados).toFixed(2)
                    }
                    // Calcule o tempo gasto
                    const tempoAtual = new Date();
                    const tempoDecorrido = (tempoAtual - this.tempoInicial) / 1000; // Em segundos
                    this.tempoGasto = tempoDecorrido.toFixed(2);
                    const resultData = [this.pratosLavados, this.tempoGasto, this.tempoMedio]
                    this.$emit('derrota', resultData) // Chama o Modal para indicar a derrota
                    return this.onStandby = true;
                }

                if (todasPilhasVazias) {
                    clearInterval(interval); // Encerra o loop
                    this.tempoGasto -= 5
                    if(this.pratosLavados > 0) {
                        this.tempoMedio = (this.tempoGasto / this.pratosLavados).toFixed(2)
                    }
                    const resultData = [this.pratosLavados, this.tempoGasto, this.tempoMedio]
                    this.$emit('vitoria', resultData) // Chama o Modal para indicar a vitoria
                    return this.onStandby = true;
                }

                // Adiciona aleatoriamente entre as pilhas
                const randomIndex = Math.floor(Math.random() * this.acumuloPratos.length);

                const pilhaSelecionada = this.acumuloPratos[randomIndex];
                if (pilhaSelecionada.pratos.length < 3) {
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

            }, 5000); // Intervalo de 5 segundos
        },
    }
}
</script>

<style scoped>
    .empilhar-pratos {
        display: block;
        flex-direction: row;
        gap: 10px;
        list-style: none;
        align-items: center;
        justify-content: center;
        color: #222;
        border-bottom: 1px solid #0056b3;
    }
    .prato-container {
        display: flex;
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
        cursor: pointer;
        transition: background-color 0.3s ease-in-out;
    }
    .start-btn button:hover {
        background-color: #0056b3;
    }
    .leave-btn {
        display: flex;
        align-items: center;
        justify-content: center;
        margin-top: 20px;
    }
    .leave-btn button {
        background-color: #e74c3c;
        color: #fff; 
        padding: 15px 20px;
        font-size: 18px;
        font-weight: bold;
        border-radius: 8px;
        cursor: pointer;
        transition: background-color 0.3s ease-in-out;
    }
    .leave-btn button:hover {
        background-color: #c0392b; /* Cor de fundo vermelho mais escura no hover */
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