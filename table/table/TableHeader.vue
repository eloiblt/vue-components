<script setup lang="ts">
import { type ColumnConfig } from '../../models/table-data.model';
import { ref, watch } from 'vue';
import { SortOrder } from '../../enums/sort-order.enum.ts';

const props = withDefaults(
  defineProps<{
    config: ColumnConfig;
    width: number;
    resetSort: boolean;
  }>(),
  {}
);

const currentSortOrder = ref<SortOrder>(SortOrder.NOSORT);
const emit = defineEmits(['sort']);

watch(
  () => props.resetSort,
  () => {
    if (props.resetSort) {
      currentSortOrder.value = SortOrder.NOSORT;
    }
  }
);

function sort() {
  switch (currentSortOrder.value) {
    case SortOrder.NOSORT:
      currentSortOrder.value = SortOrder.ASC;
      break;
    case SortOrder.ASC:
      currentSortOrder.value = SortOrder.DESC;
      break;
    case SortOrder.DESC:
      currentSortOrder.value = SortOrder.NOSORT;
      break;
  }
  emit('sort', currentSortOrder.value);
}
</script>

<template>
  <th :style="{ 'min-width': `${width}px` ?? auto, width: `${width}px` ?? auto }">
    <div class="th-container" @click="sort">
      <p>{{ props.config.name }}</p>
      <div class="sort-icon" v-if="currentSortOrder === SortOrder.ASC">
        <i class="fa-solid fa-arrow-down"></i>
      </div>
      <div class="sort-icon" v-if="currentSortOrder === SortOrder.DESC">
        <i class="fa-solid fa-arrow-up"></i>
      </div>
    </div>
  </th>
</template>

<style scoped lang="scss">
.th-container {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
}
</style>
