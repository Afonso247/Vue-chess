<template>
    <div class="modal">
      <div class="modal-content">
        <img src="/images/lost.gif" alt="Carinha triste">
        <h4>Fim de jogo!</h4>
        <p>A palavra correta era: <b>palavra</b></p>
        <button class="jogar-novamente" @click="$emit('close')">Jogar Novamente</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'Modal'
  };
  </script>
  
  <style scoped>
  /* Estilos para o modal */
  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .modal-content {
    border: 2px solid #222;
    border-radius: 10px;
    background-color: #111;
    text-align: center;
    color: #fff;
    padding: 20px;
    border-radius: 5px;
    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.5);
  }
  .modal-content img {
    max-width: 130px;
    margin-bottom: 20px;
  }
  .modal-content h4 {
    font-size: 1.5rem;
  }
  .modal-content p {
    font-size: 1.15rem;
    margin: 15px 0 30px;
    font-weight: 500;
  }
  .modal-content p b {
    color: #ff0000;
    font-weight: 600;
  }
  .modal-content button {
    padding: 12px 23px;
  }
  </style>