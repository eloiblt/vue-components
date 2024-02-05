<script setup lang="ts">
const props = withDefaults(
  defineProps<{
    size: string;
  }>(),
  {
    size: '40px',
  }
);
</script>

<template>
  <div class="pulse" :style="{ width: props.size, height: props.size }"></div>
</template>

<style lang="scss">
$offset: 187;
$duration: 1.4s;

.pulse {
  border-radius: 50%;
  animation: pulse 0.8s infinite;
  height: 20px;
  width: 20px;
  background-color: #00549a;
}

@keyframes pulse {
  0% {
    transform: scale(0.9);
    box-shadow: 0 0 0 0 rgba(#00549a, 0.7);
  }

  70% {
    transform: scale(1.1);
    box-shadow: 0 0 0 15px rgba(#00549a, 0);
  }

  100% {
    transform: scale(0.9);
    box-shadow: 0 0 0 0 rgba(#00549a, 0);
  }
}
</style>
