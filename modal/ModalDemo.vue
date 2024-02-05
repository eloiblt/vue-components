<script setup lang="ts">
import { ref } from 'vue';
import Modal from '../shared/Modal.vue';

const showModal = ref<boolean>(false);
</script>

<template>
  <button type="button" class="btn-primary" @click="showModal = !showModal">Show modal</button>

  <Modal :open="showModal" title="Home modal" @close="() => (showModal = false)">
    <template #modal-content> Modal content </template>
    <template #modal-buttons>
      <button type="button" class="btn-primary" @click="showModal = false">OK</button>
      <button type="button" @click="showModal = false">Cancel</button>
    </template>
  </Modal>
</template>

<style scoped lang="scss">
button {
  width: fit-content;
}
</style>
