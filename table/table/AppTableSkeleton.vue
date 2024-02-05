<script setup lang="ts">
import { onMounted, ref } from 'vue';
import SkeletonLoader from '../shared/SkeletonLoader.vue';

const props = withDefaults(
  defineProps<{
    linesNumber: number;
    columnsNumber: number;
  }>(),
  {
    linesNumber: 5,
    columnsNumber: 4,
  }
);

const defaultColWidth = ref<number>(0);

onMounted(() => {
  defaultColWidth.value =
    (document.querySelector('.table-container')!.clientWidth - props.columnsNumber * 20) / props.columnsNumber;
});

function randomLength() {
  return Math.floor(Math.random() * (100 - 45 + 1) + 45);
}
</script>

<template>
  <div class="table-container">
    <table>
      <tr>
        <th :style="{ 'min-width': `${defaultColWidth}px` }" v-for="index in props.columnsNumber" :key="index">
          <div class="th-container"></div>
        </th>
      </tr>

      <tr v-for="indexL in props.linesNumber" :key="indexL">
        <td v-for="indexC in props.columnsNumber" :key="indexC">
          <div class="td-container" :style="{ width: randomLength() + '%' }">
            <SkeletonLoader type="rectangle" :style="{ height: '20px' }"></SkeletonLoader>
          </div>
        </td>
      </tr>
    </table>
  </div>
</template>

<style scoped lang="scss">
.table-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 20px;

  table {
    display: block;
    overflow-x: auto;
    border-radius: 5px;
    height: fit-content;
    width: 100%;

    tr {
      background: var(--background-secondary);

      &:nth-child(odd) {
        background: var(--table-even);
      }

      td,
      th {
        padding: 15px 10px;
        text-align: left;
      }

      th {
        background: #555555;
        color: white;
        font-weight: 900;
        font-size: 15px;
        position: sticky;
        top: 0;

        &:first-of-type {
          border-top-left-radius: 5px;
        }

        &:last-of-type {
          border-top-right-radius: 5px;
        }
      }
    }
  }
}
</style>
