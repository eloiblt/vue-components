<script setup lang="ts">
import { computed, toRefs } from 'vue';

const LOADER_CSS_CLASSES = {
  rectangle: 'rounded',
  circle: 'rounded-full',
};

const isHexColor = (hexColor: string) => {
  const hex = hexColor.replace('#', '');

  return hexColor.startsWith('#') && hex.length === 6 && !isNaN(Number('0x' + hex));
};

const hexToRgb = (hex: string) => `${hex.match(/\w\w/g)?.map(x => +`0x${x}`)}`;

const props = withDefaults(
  defineProps<{
    type: 'circle' | 'rectangle';
    cssClass?: string;
    shimmerColor?: string;
  }>(),
  {
    shimmerColor: '#ffffff',
  }
);

const { type, cssClass, shimmerColor } = toRefs(props);

const shimmerStyle = computed(() => {
  const rgb = isHexColor(shimmerColor.value) ? hexToRgb(shimmerColor.value) : '#ffffff';

  return {
    backgroundImage: `linear-gradient(90deg, rgba(${rgb}, 0) 0%, rgba(${rgb}, 0.2) 20%, rgba(${rgb}, 0.5) 60%, rgba(${rgb}, 0))`,
  };
});

const loaderClass = computed(() => (cssClass?.value ? cssClass?.value : LOADER_CSS_CLASSES[type.value]));
</script>

<template>
  <div :class="[loaderClass, 'shimmer-container']">
    <div class="shimmer" :style="shimmerStyle"></div>
    <slot />
  </div>
</template>

<style scoped lang="scss">
.shimmer-container {
  background-color: rgb(209 213 219);
  position: relative;
  overflow: hidden;

  .shimmer {
    transform: translateX(-100%);
    animation: shimmer 1.4s infinite;
    position: absolute;
    inset: 0;
  }

  &.rounded {
    border-radius: 0.25rem;
  }

  &.rounded-full {
    border-radius: 9999px;
  }
}
@keyframes shimmer {
  100% {
    transform: translateX(100%);
  }
}
</style>
