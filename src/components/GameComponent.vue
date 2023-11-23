<template>
    <div class="title">
        <p v-if="brancas">Vez das <span>brancas</span></p>
        <p v-if="negras">Vez das <span>negras</span></p>
        <p v-if="reiCapturado">{{ reiCapturado }}</p>
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
            reiCapturado: '',
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
        finalizarJogo(cor) {
            this.brancas = false;
            this.negras = false;
            this.reiCapturado = `Vitória das ${cor === 'white' ? 'brancas' : 'negras'}!`
        },
        handleDragStart(event) {
            event.dataTransfer.setData('text/plain', event.target.id);
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
                    } else if (draggedPiece.moved === false) {
                        this.validarRoque(draggedPiece, droppedSquare)
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
                    // Se for um rei, ou torre, notificar que foi movido
                    if(dragged.type === 'king' || dragged.type === 'rook') {
                        this.tabuleiro[dragged.id].moved = true;
                    }
                    // Trocar os atributos "id" entre os objetos
                    const idTemp = this.tabuleiro[dragged.id].id;
                    this.tabuleiro[dragged.id].id = this.tabuleiro[dropped.id].id;
                    this.tabuleiro[dropped.id].id = idTemp;

                    // Trocar os objetos de posição na this.tabuleiro
                    if(dropped.color === 'black') {
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = { id: idTemp, type: 'empty', svg: ''};

                        if(dragged.type === 'pawn' && (dragged.id < 8 || dragged.id > 55)) {
                            this.promoverParaRainha(dragged);
                        } else {
                            this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, true, dragged.color);
                        }
                    } else {
                        const temp = this.tabuleiro[dragged.id];
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = temp;

                        if(dragged.type === 'pawn' && (dragged.id < 8 || dragged.id > 55)) {
                            this.promoverParaRainha(dragged);
                        } else {
                            this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, false, dragged.color);
                        }
                    }

                } else {
                    console.log("Não pode comer as mesmas peças");
                    return;
                }
            
            // Vez das negras
            } else if (this.negras && dragged.color === 'black') {
                if(dropped.color !== 'black') {
                    // Se for um rei, ou torre, notificar que foi movido
                    if(dragged.type === 'king' || dragged.type === 'rook') {
                        this.tabuleiro[dragged.id].moved = true;
                    }
                    // Trocar os atributos "id" entre os objetos
                    const idTemp = this.tabuleiro[dragged.id].id;
                    this.tabuleiro[dragged.id].id = this.tabuleiro[dropped.id].id;
                    this.tabuleiro[dropped.id].id = idTemp;

                    // Trocar os objetos de posição na this.tabuleiro
                    // Trocar os objetos de posição na this.tabuleiro
                    if(dropped.color === 'white') {
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = { id: idTemp, type: 'empty', svg: ''};

                        if(dragged.type === 'pawn' && (dragged.id < 8 || dragged.id > 55)) {
                            this.promoverParaRainha(dragged);
                        } else {
                            this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, true, dragged.color);
                        }
                    } else {
                        const temp = this.tabuleiro[dragged.id];
                        this.tabuleiro[dragged.id] = this.tabuleiro[dropped.id];
                        this.tabuleiro[dropped.id] = temp;

                        if(dragged.type === 'pawn' && (dragged.id < 8 || dragged.id > 55)) {
                            this.promoverParaRainha(dragged);
                        } else {
                            this.adicionarMovimentoAoHistorico(dragged.id, dragged.type, dropped.type, false, dragged.color);
                        }
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
        validarRoque(dragged, dropped) {
            let possivelTorreCurto = null;
            let possivelTorreLongo = null;

            if (this.brancas && dragged.color === 'white') {
                if (dropped.id === 62) {
                    possivelTorreCurto = this.tabuleiro.find(objeto => objeto.id === 63);
                    if (
                        possivelTorreCurto &&
                        possivelTorreCurto.type === 'rook' &&
                        !dragged.moved &&
                        !possivelTorreCurto.moved
                    ) {
                        if (this.verificarCaminhoLivre(dragged.id, possivelTorreCurto.id)) {
                            return this.realizarRoqueCurto(dragged, possivelTorreCurto);
                        }
                    }
                } else if (dropped.id === 58) {
                    possivelTorreLongo = this.tabuleiro.find(objeto => objeto.id === 56);
                    if (
                        possivelTorreLongo &&
                        possivelTorreLongo.type === 'rook' &&
                        !dragged.moved &&
                        !possivelTorreLongo.moved
                    ) {
                        if (this.verificarCaminhoLivre(possivelTorreLongo.id, dragged.id)) {
                            return this.realizarRoqueLongo(dragged, possivelTorreLongo);
                        }
                    }
                }
            } else if (this.negras && dragged.color === 'black') {
                if (dropped.id === 6) {
                    possivelTorreCurto = this.tabuleiro.find(objeto => objeto.id === 7);
                    if (
                        possivelTorreCurto &&
                        possivelTorreCurto.type === 'rook' &&
                        !dragged.moved &&
                        !possivelTorreCurto.moved
                    ) {
                        if (this.verificarCaminhoLivre(dragged.id, possivelTorreCurto.id)) {
                            return this.realizarRoqueCurto(dragged, possivelTorreCurto);
                        }
                    }
                } else if (dropped.id === 2) {
                    possivelTorreLongo = this.tabuleiro.find(objeto => objeto.id === 0);
                    if (
                        possivelTorreLongo &&
                        possivelTorreLongo.type === 'rook' &&
                        !dragged.moved &&
                        !possivelTorreLongo.moved
                    ) {
                        if (this.verificarCaminhoLivre(possivelTorreLongo.id, dragged.id)) {
                            return this.realizarRoqueLongo(dragged, possivelTorreLongo);
                        }
                    }
                }
            }
            return console.log('Movimento inválido para o rei');
        },
        verificarCaminhoLivre(start, end) {
            const min = Math.min(start, end);
            const max = Math.max(start, end);

            for (let i = min + 1; i < max; i++) {
                const square = this.tabuleiro.find(objeto => objeto.id === i);
                if (square.type !== 'empty') {
                    return false;
                }
            }
            return true;
        },
        realizarRoqueCurto(rei, torre) {
            const novaPosicaoRei = rei.id + 2;
            const novaPosicaoTorre = rei.id + 1;
            rei.moved = true;
            torre.moved = true;

            // Atualizar as posições no tabuleiro
            this.tabuleiro[novaPosicaoRei] = rei;
            this.tabuleiro[novaPosicaoTorre] = torre;
            this.tabuleiro[rei.id] = { id: rei.id, type: 'empty', svg: '' };
            this.tabuleiro[torre.id] = { id: torre.id, type: 'empty', svg: '' };

            // Atualizar os dados das peças
            rei.id = novaPosicaoRei;
            torre.id = novaPosicaoTorre;

            this.adicionarRoque(false);
        },
        realizarRoqueLongo(rei, torre) {
            const novaPosicaoRei = rei.id - 2;
            const novaPosicaoTorre = rei.id - 1;
            rei.moved = true;
            torre.moved = true;

            // Atualizar as posições no tabuleiro
            this.tabuleiro[novaPosicaoRei] = rei;
            this.tabuleiro[novaPosicaoTorre] = torre;
            this.tabuleiro[rei.id] = { id: rei.id, type: 'empty', svg: '' };
            this.tabuleiro[torre.id] = { id: torre.id, type: 'empty', svg: '' };

            // Atualizar os dados das peças
            rei.id = novaPosicaoRei;
            torre.id = novaPosicaoTorre;

            this.adicionarRoque(true);
        },
        promoverParaRainha(peao) {
            // Promove o peão para uma rainha
            this.tabuleiro[peao.id].type = 'queen';
            if(peao.color === 'white') {
                this.tabuleiro[peao.id].svg = '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 512 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M256 96a48 48 0 1 0 0-96 48 48 0 1 0 0 96zm-95.2-8c-18.1 0-31.3 12.8-35.6 26.9c-8 26.2-32.4 45.2-61.2 45.2c-10 0-19.4-2.3-27.7-6.3c-7.6-3.7-16.7-3.3-24 1.2C.7 162.1-3.1 177.1 3.7 188.9L97.6 352H153l-83-144.1c40.5-2.2 75.3-25.9 93.1-59.8c22 26.8 55.4 43.9 92.8 43.9s70.8-17.1 92.8-43.9c17.8 34 52.6 57.7 93.1 59.8L359 352h55.4l93.9-163.1c6.8-11.7 3-26.7-8.6-33.8c-7.3-4.5-16.4-4.9-24-1.2c-8.4 4-17.7 6.3-27.7 6.3c-28.8 0-53.2-19-61.2-45.2C382.5 100.8 369.3 88 351.2 88c-14.5 0-26.3 8.5-32.4 19.3c-12.4 22-35.9 36.7-62.8 36.7s-50.4-14.8-62.8-36.7C187.1 96.5 175.4 88 160.8 88zM133.2 432H378.8l16.6 32H116.7l16.6-32zm283.7-30.7c-5.5-10.6-16.5-17.3-28.4-17.3h-265c-12 0-22.9 6.7-28.4 17.3L68.6 452.5c-3 5.8-4.6 12.2-4.6 18.7c0 22.5 18.2 40.8 40.8 40.8H407.2c22.5 0 40.8-18.2 40.8-40.8c0-6.5-1.6-12.9-4.6-18.7l-26.5-51.2z"/></svg>'
            } else {
                this.tabuleiro[peao.id].svg = '<svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 512 512"><!--! Font Awesome Free 6.4.2 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M256 0a56 56 0 1 1 0 112A56 56 0 1 1 256 0zM134.1 143.8c3.3-13 15-23.8 30.2-23.8c12.3 0 22.6 7.2 27.7 17c12 23.2 36.2 39 64 39s52-15.8 64-39c5.1-9.8 15.4-17 27.7-17c15.3 0 27 10.8 30.2 23.8c7 27.8 32.2 48.3 62.1 48.3c10.8 0 21-2.7 29.8-7.4c8.4-4.4 18.9-4.5 27.6 .9c13 8 17.1 25 9.2 38L399.7 400H384 343.6 168.4 128 112.3L5.4 223.6c-7.9-13-3.8-30 9.2-38c8.7-5.3 19.2-5.3 27.6-.9c8.9 4.7 19 7.4 29.8 7.4c29.9 0 55.1-20.5 62.1-48.3zM256 224l0 0 0 0h0zM112 432H400l41.4 41.4c4.2 4.2 6.6 10 6.6 16c0 12.5-10.1 22.6-22.6 22.6H86.6C74.1 512 64 501.9 64 489.4c0-6 2.4-11.8 6.6-16L112 432z"/></svg>'
            }

            this.adicionarProm(peao);
        },
        adicionarMovimentoAoHistorico(destino, tipoOrigem, tipoDestino, captura, cor) {
            let pecaOrig = ''
            if(tipoOrigem === 'knight') {
                pecaOrig = tipoOrigem.charAt(1).toUpperCase();
            } else if(tipoOrigem !== 'pawn') {
                pecaOrig = tipoOrigem.charAt(0).toUpperCase();
            }
            let localDest = ''
            if(tipoDestino === 'knight') {
                localDest = tipoDestino.charAt(1).toUpperCase();
            } else if(tipoDestino !== 'pawn' && tipoDestino !== 'empty') {
                localDest = tipoDestino.charAt(0).toUpperCase();
            }
            let movimento = '';

            if (captura) {
                movimento = `${pecaOrig}x${localDest}${this.numConversion(destino)}`;
                console.log(`${tipoOrigem} ${cor} capturou ${tipoDestino} na posição ${destino}`)
            } else {
                movimento = `${pecaOrig}${localDest}${this.numConversion(destino)}`;
                console.log(`${tipoOrigem} ${cor} moveu-se na posição ${destino}`)
            }

            // Adiciona ao histórico de movimentos
            if (this.brancas) {
                this.historicoMovimentos.push(` ${this.historicoMovimentos.length + 1}. ${movimento}`);
            } else {
                const ultimoMovimento = this.historicoMovimentos[this.historicoMovimentos.length - 1];
                this.historicoMovimentos[this.historicoMovimentos.length - 1] = ultimoMovimento + ` ${movimento}`;
            }

            if (tipoDestino === 'king') {
                return this.finalizarJogo(cor);
            }

            console.log(this.tabuleiro)
            
            this.passarVez();
        },
        adicionarRoque(tipo) {
            let roque = ''
            if(tipo) {
                roque = 'O-O-O'
            } else {
                roque = 'O-O'
            }

            // Adiciona ao histórico de movimentos
            if (this.brancas) {
                this.historicoMovimentos.push(` ${this.historicoMovimentos.length + 1}. ${roque}`);
                console.log(tipo ? 'As brancas realizaram um roque longo' : 'As brancas realizaram um roque curto');
            } else {
                const ultimoMovimento = this.historicoMovimentos[this.historicoMovimentos.length - 1];
                this.historicoMovimentos[this.historicoMovimentos.length - 1] = ultimoMovimento + ` ${roque}`;
                console.log(tipo ? 'As negras realizaram um roque longo' : 'As negras realizaram um roque curto');
            }
            
            this.passarVez();
        },
        adicionarProm(peao) {
            // Adiciona a ação ao histórico de movimentos
            const prom = `${this.numConversion(peao.id)}=Q`;

            if (this.brancas) {
                this.historicoMovimentos.push(` ${this.historicoMovimentos.length + 1}. ${prom}`);
                console.log(`pawn white na posição ${peao.id} foi promovido a queen`);
            } else {
                const ultimoMovimento = this.historicoMovimentos[this.historicoMovimentos.length - 1];
                this.historicoMovimentos[this.historicoMovimentos.length - 1] = ultimoMovimento + ` ${prom}`;
                console.log(`pawn black na posição ${peao.id} foi promovido a queen`);
            }

            this.passarVez();
        },
        numConversion(num) {
            const colunas = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h'];
            const linha = 8 - Math.floor(num / 8);
            const coluna = colunas[num % 8];
            return coluna + linha;
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