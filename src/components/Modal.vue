<template>
    <div class="modal">
      <div class="modal-content">
        <div class="text-content">
          <slot></slot>
        </div>
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
  
  .modal-content .text-content {
    margin-bottom: 15px;
  }
  .modal-content button {
    padding: 12px 23px;
  }
  </style>