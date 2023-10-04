<script>
import Stats from './components/StatsComponent.vue';
import Game from './components/GameComponent.vue';
import Modal from './components/Modal.vue';

  export default {
    name: "App",
    data() {
      return {
        mostrarModal: false,
        modalText: '',
        dataDump: null // 0: pratos lavados, 1: tempo total, 2: tempo médio
      }
    },
    methods: {
      modalVitoria(resultData) {
        this.mostrarModal = true;
        this.modalText = "Parabéns! Você venceu."
        this.dataDump = resultData
      },
      modalDerrota(resultData) {
        this.mostrarModal = true;
        this.modalText = "Que pena! Você perdeu."
        this.dataDump = resultData
      },
      fecharModal() {
        this.mostrarModal = false;
        return window.location.reload();
      }
    },
    components: {
      Stats,
      Game,
      Modal
    }
  }
</script>

<template>
  <div class="container">
    <Stats />
    <Game @vitoria="modalVitoria" @derrota="modalDerrota"/>
    <Modal v-if="mostrarModal" @close="fecharModal">
      <div>
        <h2>
          {{ modalText }}
        </h2>
        <p>
          Pratos lavados: {{ dataDump[0] }}
        </p>
        <p>
          Tempo total: {{ dataDump[1] }}seg
        </p>
        <p>
          Tempo médio por prato: {{ dataDump[2] }}seg
        </p>
      </div>
    </Modal>
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
  align-items: center;
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
