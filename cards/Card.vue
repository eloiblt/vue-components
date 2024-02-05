<script setup lang="ts">
import SkeletonLoader from './SkeletonLoader.vue';

const props = withDefaults(
  defineProps<{
    title?: string;
    skeleton?: boolean;
  }>(),
  {
    skeleton: false,
  }
);
</script>

<template>
  <div class="card-wrapper">
    <div class="card-content card" v-if="!props.skeleton">
      <div class="card-title">{{ title }}</div>
      <div class="card-text"><slot name="card-text"></slot></div>
    </div>

    <div class="card-content skeleton" v-if="props.skeleton">
      <SkeletonLoader class="skull" type="rectangle" :style="{ height: '40px', margin: '0px' }"></SkeletonLoader>
      <SkeletonLoader class="skull" type="rectangle" :style="{ height: '15px', margin: '2px 10px' }"></SkeletonLoader>
      <SkeletonLoader class="skull" type="rectangle" :style="{ height: '15px', margin: '2px 10px' }"></SkeletonLoader>
      <SkeletonLoader class="skull" type="rectangle" :style="{ height: '15px', margin: '2px 10px' }"></SkeletonLoader>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.card-content {
  display: flex;
  flex-direction: column;
  border-radius: 5px;
  overflow: hidden;
  font-weight: 900;
  width: 200px;

  .card-title {
    background: var(--primary-color);
    color: var(--text-tertiary);
    align-items: center;
    padding: 10px;
  }

  .card-text {
    padding: 10px;
    background: var(--card-background);
    flex: 1;
  }

  * {
    white-space: break-spaces;
  }

  &.skeleton {
    gap: 5px;
    padding-bottom: 10px;
    background: var(--card-background);
  }

  &:not(.skeleton) {
    height: 100%;
  }
}
</style>
