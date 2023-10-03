<script>
import Hangman from './components/HangmanComponent.vue';
import Game from './components/GameComponent.vue';
import Modal from './components/GameModal.vue';

  export default {
    name: "App",
    data() {
      return {
        mostrarModal: false
      }
    },
    methods: {
      abrirModal() {
        this.mostrarModal = true;
      },
      fecharModal() {
        this.mostrarModal = false;
      }
    },
    components: {
      Hangman,
      Game,
      Modal
    }
  }
</script>

<template>
  <div class="container">
    <Hangman />
    <Game />
    <Modal v-if="mostrarModal" @close="fecharModal"/>
  </div>

</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

.container {
  width: 1200px;
  background-color: #fff;
  display: flex;
  gap: 70px;
  align-items: flex-end;
  padding: 60px 40px;
  border-radius: 10px;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
