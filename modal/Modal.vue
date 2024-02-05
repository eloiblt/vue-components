<script setup lang="ts">
import { ref, watch } from 'vue';

const props = withDefaults(
  defineProps<{
    title: string;
    open: boolean;
  }>(),
  {
    open: false,
  }
);

const emit = defineEmits(['close']);
const isOpen = ref<boolean>(props.open);

watch(
  () => props.open,
  () => {
    isOpen.value = props.open;
  }
);

function close() {
  isOpen.value = false;
  emit('close');
}
</script>

<template>
  <Transition name="fade">
    <div class="modal-container" v-if="isOpen">
      <div class="modal">
        <div class="header">
          <h2>{{ props.title }}</h2>
          <button type="button" class="btn-no-style" @click="close">
            <i class="fa-solid fa-xmark"></i>
          </button>
        </div>
        <div class="content"><slot name="modal-content"></slot></div>
        <div class="buttons"><slot name="modal-buttons"></slot></div>
      </div>
    </div>
  </Transition>
</template>

<style scoped lang="scss">
.modal-container {
  position: fixed;
  inset: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: var(--modal-background);
  z-index: 1;

  .modal {
    background: var(--card-background);
    border-radius: 5px;
    min-width: 500px;
    margin-top: -300px;

    .header {
      border-bottom: 1px solid grey;
      display: flex;
      align-items: center;
      padding: 10px 20px;

      h2 {
        font-size: 20px;
      }

      button {
        margin-left: auto;
        font-size: 15px;
        padding: 5px;
        color: var(--text-primary);
      }
    }

    .content {
      padding: 15px 20px;
    }

    .buttons {
      border-top: 1px solid grey;
      display: flex;
      align-items: center;
      justify-content: flex-end;
      gap: 10px;
      padding: 15px 20px;
    }
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
