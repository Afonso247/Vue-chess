<template>
    <div class="title">
        <p v-if="brancas">Vez das <span>brancas</span></p>
        <p v-if="negras">Vez das <span>negras</span></p>
    </div>
    <div class="chessboard">
        <div v-for="(tabs, index) in tabuleiro" :key="index">
            <div 
                :class="{ 'quadrado': true, 'beige': isBeige(index), 'gray': isGray(index) }" 
                :id="tabs.id" 
                :data-type="tabs.type"
                :data-color="tabs.color"
                draggable="true"
                @dragstart="handleDragStart"
                @dragover="handleDragOver"
                @drop="handleDrop"
                v-html="tabs.svg"
            ></div>
        </div>
    </div>
    <div class="info">
        <p v-for="(historico, index) in historicoMovimentos" :key="index">{{ historico }}</p>
    </div>
</template>

<script>
import tabs from '../data/data';

    export default {
    name: "Game",
    data() {
        return {
            brancas: false,
            negras: false,
            width: 8,
            tabuleiro: tabs,
            historicoMovimentos: []
        }
    },
    methods: {
        iniciarPartida() {
            this.brancas = true;
        },
        passarVez() {
            this.brancas = !this.brancas;
            this.negras = !this.negras;
        },
        handleDragStart(event) {
            event.dataTransfer.setData('text/plain', event.target.id);
            console.log(event.target.id)
            console.log(event)
        },
        handleDragOver(event) {
            event.preventDefault();
        },
        handleDrop(event) {
            // Evita o comportamento padrão do navegador
            event.stopPropagation();
            event.preventDefault();

            const draggedId = event.dataTransfer.getData('text/plain');

            // Obtém o ID do elemento alvo
            const droppedId = event.target.closest('.quadrado').id;

            this.moverPeca(parseInt(draggedId), parseInt(droppedId));
        },
        moverPeca(draggedId, droppedId) {

            const draggedPiece = this.tabuleiro.find(piece => piece.id === draggedId);
            const droppedSquare = this.tabuleiro.find(square => square.id === droppedId);

            // Verificar se os índices são válidos
            if (draggedPiece < 0 || draggedPiece >= this.tabuleiro.length ||
                droppedSquare < 0 || droppedSquare >= this.tabuleiro.length || 
                draggedPiece == droppedSquare) {
                console.log('Índices inválidos');
                return;
            }

            switch (draggedPiece.type) {
                case 'pawn':
                    if (this.validarMovimentoPeao(draggedPiece, droppedSquare)) {
                        this.trocarPecas(draggedPiece, droppedSquare);
                    } else {
                        console.log('Movimento inválido para o peão');
                    }
                    break;
                case 'bishop':
                    if (this.validarMovimentoBispo(draggedPiece, droppedSquare)) {
                        this.trocarPecas(draggedPiece, droppedSquare);
                    } else {
                        console.log('Movimento inválido para o bispo');
                    }
                    break;
                case 'rook':
                    if (this.validarMovimentoTorre(draggedPiece, droppedSquare)) {
                        this.trocarPecas(draggedPiece, droppedSquare);
                    } else {
                        console.log('Movimento inválido para a torre');
                    }
                    break;
                case 'knight':
                    if (this.validarMovimentoCavalo(draggedPiece, droppedSquare)) {
                        this.trocarPecas(draggedPiece, droppedSquare);
                    } else {
                        console.log('Movimento inválido para o cavalo');
                    }
                    break;
                case 'queen':
                    if (this.validarMovimentoRainha(draggedPiece, droppedSquare)) {
                        this.trocarPecas(draggedPiece, droppedSquare);
                    } else {
                        console.log('Movimento inválido para a rainha');
                    }
                    break;
                case 'king':
                    if (this.validarMovimentoRei(draggedPiece, droppedSquare)) {
                        this.trocarPecas(draggedPiece, droppedSquare);
                    } else {
                        console.log('Movimento inválido para o rei');
                    }
                    break;
                default:
                    console.log('movendo nada')
                    return;
            }

            // this.trocarPecas(draggedPiece, droppedSquare);
        },
        trocarPecas(dragged, dropped) {
            // Vez das brancas
            if(this.brancas && dragged.color === 'white') {
                if(dropped.color !== 'white') {
                    // Trocar os atributos "id" entre os objetos
                    const idTemp = this.tabuleiro[dragged.id].id;
                    this.tabuleiro[dragged.id].id = this.tabuleiro[dropped.id].id;
                    this.tabuleiro[dropped.id].id = idTemp;

                    // Trocar os objetos de posição na this.tabuleiro
                    if(dropped.color === 'black') {
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = { id: idTemp, type: 'empty', svg: ''};

                        this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, true);
                    } else {
                        const temp = this.tabuleiro[dragged.id];
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = temp;

                        this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, false);
                    }

                } else {
                    console.log("Não pode comer as mesmas peças");
                    return;
                }
            
            // Vez das negras
            } else if (this.negras && dragged.color === 'black') {
                if(dropped.color !== 'black') {
                    // Trocar os atributos "id" entre os objetos
                    const idTemp = this.tabuleiro[dragged.id].id;
                    this.tabuleiro[dragged.id].id = this.tabuleiro[dropped.id].id;
                    this.tabuleiro[dropped.id].id = idTemp;

                    // Trocar os objetos de posição na this.tabuleiro
                    // Trocar os objetos de posição na this.tabuleiro
                    if(dropped.color === 'white') {
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = { id: idTemp, type: 'empty', svg: ''};

                        this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, true);
                    } else {
                        const temp = this.tabuleiro[dragged.id];
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = temp;

                        this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, false);
                    }

                } else {
                    console.log("Não pode comer as mesmas peças");
                    return;
                }
            }
        },
        validarMovimentoPeao(dragged, dropped) {
            const distance = Math.abs(dragged.id - dropped.id);
            const direction = dragged.color === 'white' ? -1 : 1;

            // Movimento de duas casas para frente no primeiro movimento
            if (distance === 16 && dropped.type === 'empty') {
                // Verifica se é o primeiro movimento do peão
                if ((dragged.color === 'white' && dragged.id > 47) || (dragged.color === 'black' && dragged.id < 16)) {
                    // Verifica se não há peças no caminho
                    const middleSquareId = dragged.id + 8 * direction;
                    console.log(middleSquareId);
                    const middleSquare = this.tabuleiro.find(square => square.id === middleSquareId);
                    if (middleSquare.type === 'empty') {
                        return true;
                    }
                }
            }

            // Movimento de uma casa para frente
            if (distance === 8 && dropped.type === 'empty') {
                return true;
            }

            // Captura na diagonal
            if (distance === 9 || distance === 7) {
                const diagonalPiece = this.tabuleiro.find(piece => piece.id === dropped.id);
                if(diagonalPiece.type === 'empty') {
                    return false;
                }
                return diagonalPiece && diagonalPiece.color !== dragged.color;
            }

            return false;
        },
        validarMovimentoBispo(dragged, dropped) {
            const distanceRow = Math.abs(Math.floor(dragged.id / 8) - Math.floor(dropped.id / 8));
            const distanceCol = Math.abs((dragged.id % 8) - (dropped.id % 8));

            // Verificar se o movimento é diagonal (mesma distância nas linhas e colunas)
            if (distanceRow === distanceCol) {
                const rowDirection = dragged.id < dropped.id ? 1 : -1;
                const colDirection = dragged.id % 8 < dropped.id % 8 ? 1 : -1;

                // Verificar se não há peças no caminho do bispo
                for (let i = 1; i < distanceRow; i++) {
                    const intermediateSquareId = dragged.id + i * 8 * rowDirection + i * colDirection;
                    const intermediateSquare = this.tabuleiro.find(square => square.id === intermediateSquareId);
                    if (intermediateSquare.type !== 'empty') {
                        return false; // Existe uma peça bloqueando o caminho
                    }
                }

                const destinationSquare = this.tabuleiro.find(square => square.id === dropped.id);
                return destinationSquare.type === 'empty' || destinationSquare.color !== dragged.color;
            }

            return false; // Movimento inválido para o bispo
        },
        validarMovimentoTorre(dragged, dropped) {
            const distanceRow = Math.abs(Math.floor(dragged.id / 8) - Math.floor(dropped.id / 8));
            const distanceCol = Math.abs((dragged.id % 8) - (dropped.id % 8));

            // Verificar se o movimento é na mesma linha ou coluna
            if ((distanceRow === 0 && distanceCol !== 0) || (distanceCol === 0 && distanceRow !== 0)) {
                const rowDirection = dragged.id < dropped.id ? 1 : -1;
                const colDirection = dragged.id % 8 < dropped.id % 8 ? 1 : -1;

                // Verificar se não há peças no caminho da torre
                if (distanceRow > 0) {
                    for (let i = 1; i < distanceRow; i++) {
                        const intermediateSquareId = dragged.id + i * 8 * rowDirection;
                        const intermediateSquare = this.tabuleiro.find(square => square.id === intermediateSquareId);
                        if (intermediateSquare.type !== 'empty') {
                            return false; // Existe uma peça bloqueando o caminho
                        }
                    }
                } else {
                    for (let i = 1; i < distanceCol; i++) {
                        const intermediateSquareId = dragged.id + i * colDirection;
                        const intermediateSquare = this.tabuleiro.find(square => square.id === intermediateSquareId);
                        if (intermediateSquare.type !== 'empty') {
                            return false; // Existe uma peça bloqueando o caminho
                        }
                    }
                }

                const destinationSquare = this.tabuleiro.find(square => square.id === dropped.id);
                return destinationSquare.type === 'empty' || destinationSquare.color !== dragged.color;
            }

            return false; // Movimento inválido para a torre
        },
        validarMovimentoCavalo(dragged, dropped) {
            const distanceRow = Math.abs(Math.floor(dragged.id / 8) - Math.floor(dropped.id / 8));
            const distanceCol = Math.abs((dragged.id % 8) - (dropped.id % 8));

            // Verificar se o movimento é em forma de "L" (2 espaços em uma direção e 1 espaço perpendicular)
            if ((distanceRow === 2 && distanceCol === 1) || (distanceRow === 1 && distanceCol === 2)) {
                const destinationSquare = this.tabuleiro.find(square => square.id === dropped.id);
                return destinationSquare.type === 'empty' || destinationSquare.color !== dragged.color;
            }

            return false; // Movimento inválido para o cavalo
        },
        validarMovimentoRainha(dragged, dropped) {
            const distanceRow = Math.abs(Math.floor(dragged.id / 8) - Math.floor(dropped.id / 8));
            const distanceCol = Math.abs((dragged.id % 8) - (dropped.id % 8));

            // Verificar se o movimento é na mesma linha, coluna ou diagonal
            if ((distanceRow === 0 && distanceCol !== 0) || (distanceCol === 0 && distanceRow !== 0) || (distanceRow === distanceCol)) {
                const rowDirection = dragged.id < dropped.id ? 1 : -1;
                const colDirection = dragged.id % 8 < dropped.id % 8 ? 1 : -1;

                // Verificar se não há peças no caminho
                if (distanceRow > 0 && distanceCol === 0) { // Movimento na mesma coluna
                    for (let i = 1; i < distanceRow; i++) {
                        const intermediateSquareId = dragged.id + i * 8 * rowDirection;
                        const intermediateSquare = this.tabuleiro.find(square => square.id === intermediateSquareId);
                        if (intermediateSquare.type !== 'empty') {
                            return false; // Existe uma peça bloqueando o caminho
                        }
                    }
                } else if (distanceCol > 0 && distanceRow === 0) { // Movimento na mesma linha
                    for (let i = 1; i < distanceCol; i++) {
                        const intermediateSquareId = dragged.id + i * colDirection;
                        const intermediateSquare = this.tabuleiro.find(square => square.id === intermediateSquareId);
                        if (intermediateSquare.type !== 'empty') {
                            return false; // Existe uma peça bloqueando o caminho
                        }
                    }
                } else if (distanceRow === distanceCol) { // Movimento diagonal
                    for (let i = 1; i < distanceRow; i++) {
                        const intermediateSquareId = dragged.id + i * 8 * rowDirection + i * colDirection;
                        const intermediateSquare = this.tabuleiro.find(square => square.id === intermediateSquareId);
                        if (intermediateSquare.type !== 'empty') {
                            return false; // Existe uma peça bloqueando o caminho
                        }
                    }
                }

                const destinationSquare = this.tabuleiro.find(square => square.id === dropped.id);
                return destinationSquare.type === 'empty' || destinationSquare.color !== dragged.color;
            }

            return false; // Movimento inválido para a rainha
        },
        validarMovimentoRei(dragged, dropped) {
            const distanceRow = Math.abs(Math.floor(dragged.id / 8) - Math.floor(dropped.id / 8));
            const distanceCol = Math.abs((dragged.id % 8) - (dropped.id % 8));

            // Verificar se o movimento é de apenas uma casa em qualquer direção
            if ((distanceRow === 1 && distanceCol === 0) || (distanceRow === 0 && distanceCol === 1) || (distanceRow === 1 && distanceCol === 1)) {
                const destinationSquare = this.tabuleiro.find(square => square.id === dropped.id);
                return destinationSquare.type === 'empty' || destinationSquare.color !== dragged.color;
            }

            return false; // Movimento inválido para o rei
        },
        adicionarMovimentoAoHistorico(destino, tipoOrigem, tipoDestino, captura) {
            let pecaOrig = ''
            if(tipoOrigem !== 'pawn') {
                pecaOrig = tipoOrigem.charAt(0).toUpperCase();
            }
            let localDest = ''
            if(tipoDestino !== 'pawn' && tipoDestino !== 'empty') {
                localDest = tipoDestino.charAt(0).toUpperCase();
            }
            let movimento = '';

            if (captura) {
                movimento = `${pecaOrig}x${localDest}${destino}`;
            } else {
                movimento = `${pecaOrig}${localDest}${destino}`;
            }

            // Adiciona ao histórico de movimentos
            if (this.brancas) {
                this.historicoMovimentos.push(` ${this.historicoMovimentos.length + 1}. ${movimento}`);
            } else {
                const ultimoMovimento = this.historicoMovimentos[this.historicoMovimentos.length - 1];
                this.historicoMovimentos[this.historicoMovimentos.length - 1] = ultimoMovimento + ` ${movimento}`;
            }
            
            this.passarVez();
        },
        isBeige(index) {
            const row = Math.floor(index / this.width);
            const col = index % this.width;
            return (row + col) % 2 === 0;
        },
        isGray(index) {
            return !this.isBeige(index);
        }
    },
    mounted() {
        this.iniciarPartida();
    }
}
</script>

<style scoped>
    .title {
        color: black;
    }
    .chessboard {
        width: 320px;
        height: 320px;
        display: flex;
        flex-wrap: wrap;
    }
    .quadrado {
        width: 40px;
        height: 40px;
        border: 1px solid black;
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .info {
        color: black;
    }
    .beige {
        background-color: #f0d9b5; /* Cor bege */
    }
    .gray {
        background-color: #a9a9a9; /* Cor cinza */
    }
</style>