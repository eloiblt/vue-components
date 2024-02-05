<script setup lang="ts">
import { ref } from 'vue';

const props = withDefaults(
  defineProps<{
    menu: string[];
  }>(),
  {}
);
const openedMenu = ref<Set<string>>(new Set<string>());
</script>

<template>
  <div class="accordion-container">
    <div class="item" v-for="(item, index) in props.menu" :key="index">
      <div class="title" @click="openedMenu.has(item) ? openedMenu.delete(item) : openedMenu.add(item)">
        <Transition name="fade">
          <div class="title-icon" v-if="!openedMenu.has(item)">
            <i class="fa-solid fa-circle-plus"></i>
          </div>
        </Transition>
        <Transition name="fade">
          <div class="title-icon" v-if="openedMenu.has(item)">
            <i class="fa-solid fa-circle-minus"></i>
          </div>
        </Transition>

        <p>{{ item }}</p>
      </div>
      <div class="content" :class="{ open: openedMenu.has(item) }">
        <slot :name="item"></slot>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.accordion-container {
  display: flex;
  flex-direction: column;
  gap: 20px;

  .item {
    display: flex;
    flex-direction: column;

    .title {
      font-weight: 900;
      cursor: pointer;
      position: relative;
      height: 20px;
      display: flex;
      align-items: center;
      width: 100%;
      user-select: none;

      .title-icon {
        position: absolute;
        inset: 0;
        color: var(--primary-color);
        font-size: 20px;
      }

      > p {
        margin-left: 30px;
        font-size: 17px;
      }
    }

    .content {
      max-height: 0;
      overflow: hidden;
      transition: max-height 0.3s ease-out;
      margin-left: 30px;

      &:before {
        content: '';
        display: block;
        height: 20px;
      }

      &.open {
        max-height: 500px;
        transition: max-height 0.3s ease-in;
      }
    }
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s linear;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
