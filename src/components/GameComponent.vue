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
                v-html="tabs.svg"
            ></div>
        </div>
    </div>
    <div class="info">
        <p>info</p>
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
            tabuleiro: tabs
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

            // Vez das brancas
            if(this.brancas && draggedPiece.color === 'white') {
                if(droppedSquare.color !== 'white') {
                    // Trocar os atributos "id" entre os objetos
                    const idTemp = this.tabuleiro[draggedPiece.id].id;
                    this.tabuleiro[draggedPiece.id].id = this.tabuleiro[droppedSquare.id].id;
                    this.tabuleiro[droppedSquare.id].id = idTemp;

                    // Trocar os objetos de posição na this.tabuleiro
                    if(droppedSquare.color === 'black') {
                        this.tabuleiro[draggedPiece.id] = this.tabuleiro[droppedSquare.id];
                        this.tabuleiro[droppedSquare.id] = { id: idTemp, type: 'empty', svg: ''};
                    } else {
                        const temp = this.tabuleiro[draggedPiece.id];
                        this.tabuleiro[draggedPiece.id] = this.tabuleiro[droppedSquare.id];
                        this.tabuleiro[droppedSquare.id] = temp;
                    }

                    this.passarVez()
                } else {
                    console.log("Não pode comer as mesmas peças");
                    return;
                }
            
            // Vez das negras
            } else if (this.negras && draggedPiece.color === 'black') {
                if(droppedSquare.color !== 'black') {
                    // Trocar os atributos "id" entre os objetos
                    const idTemp = this.tabuleiro[draggedPiece.id].id;
                    this.tabuleiro[draggedPiece.id].id = this.tabuleiro[droppedSquare.id].id;
                    this.tabuleiro[droppedSquare.id].id = idTemp;

                    // Trocar os objetos de posição na this.tabuleiro
                    // Trocar os objetos de posição na this.tabuleiro
                    if(droppedSquare.color === 'white') {
                        this.tabuleiro[draggedPiece.id] = this.tabuleiro[droppedSquare.id];
                        this.tabuleiro[droppedSquare.id] = { id: idTemp, type: 'empty', svg: ''};
                    } else {
                        const temp = this.tabuleiro[draggedPiece.id];
                        this.tabuleiro[draggedPiece.id] = this.tabuleiro[droppedSquare.id];
                        this.tabuleiro[droppedSquare.id] = temp;
                    }

                    this.passarVez()
                } else {
                    console.log("Não pode comer as mesmas peças");
                    return;
                }
            }
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
        const squares = document.querySelectorAll('.quadrado');

        squares.forEach(square => {
            square.addEventListener('dragstart', this.handleDragStart);
            square.addEventListener('dragover', this.handleDragOver);
            square.addEventListener('drop', this.handleDrop);
        });
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
    .beige {
        background-color: #f0d9b5; /* Cor bege */
    }
    .gray {
        background-color: #a9a9a9; /* Cor cinza */
    }
</style>