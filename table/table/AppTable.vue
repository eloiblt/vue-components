<script setup lang="ts">
import TableRow from './TableRow.vue';
import { type ColumnConfig } from '../../models/table-data.model.ts';
import TableHeader from './TableHeader.vue';
import { onMounted, onUpdated, ref, watch } from 'vue';
import { SortOrder } from '../../enums/sort-order.enum.ts';
import { ColsTypeEnum } from '../../enums/cols-type.enum.ts';
import Paginate from '../shared/Paginate.vue';
import { useI18n } from 'vue-i18n';

const props = withDefaults(
  defineProps<{
    totalCount?: number; // not required if addPagination = false
    data: any[];
    config: ColumnConfig[];
    fixedFirstCol?: boolean;
    fixedLastCol?: boolean;
    addPagination?: boolean;
    emitOnSort?: boolean;
  }>(),
  {
    fixedFirstCol: false,
    fixedLastCol: false,
    addPagination: true,
    emitOnSort: false,
  }
);

const { t } = useI18n();

const sortedColum = ref<ColumnConfig>();
const lines = ref<any[]>([]);
const pagesCount = ref<number>(0);
const forceNavigationPage = ref<number>(1);
const defaultColWidth = ref<number>(0);
const possiblePageLength = ref<number[]>([5, 10, 15, 50]);
const currentPage = ref<number>(1);

// the 'sort' emit is only used if frontEndSearch = false
const emit = defineEmits(['pageSize', 'page', 'sort']);

onMounted(() => {
  lines.value = [...props.data];
});

onUpdated(() => {
  updateWidth();
});

watch(
  () => props.data,
  () => {
    lines.value = [...props.data];
    updateWidth();

    if (!props.addPagination || !props.totalCount) return;
    pagesCount.value = Math.ceil(props.totalCount / lines.value.length);
  }
);

function updateWidth() {
  const cellPaddingLeftRight = 10 * 2;

  const userDefinedWidth =
    props.config
      .filter(c => c.widthPx)
      .map(c => c.widthPx! + cellPaddingLeftRight)
      .reduce((a, b) => a + b, 0) ?? 0;

  const columnsWithoutWidth = props.config.filter(c => !c.widthPx).length;

  defaultColWidth.value =
    (document.querySelector('.table-container')!.clientWidth -
      userDefinedWidth -
      columnsWithoutWidth * cellPaddingLeftRight) /
    columnsWithoutWidth;
}

function sort(columnConfig: ColumnConfig, order: SortOrder) {
  sortedColum.value = columnConfig;

  if (props.emitOnSort) {
    emit('sort', { columnConfig, order });
    return;
  }

  if (order === SortOrder.NOSORT) {
    lines.value = [...props.data];
    return;
  }

  const property = columnConfig.property!;
  if (columnConfig.type === ColsTypeEnum.text) {
    if (order === SortOrder.ASC) {
      lines.value.sort((a, b) => {
        if (a[property] === null || a[property].trim() === '') return 1;
        if (b[property] === null || b[property].trim() === '') return -1;
        return a[property]?.localeCompare(b[property]);
      });
    } else {
      lines.value.sort((a, b) => {
        if (a[property] === null || a[property].trim() === '') return 1;
        if (b[property] === null || b[property].trim() === '') return -1;
        return b[property]?.localeCompare(a[property]);
      });
    }
  }

  if (columnConfig.type === ColsTypeEnum.decimal) {
    if (order === SortOrder.ASC) {
      lines.value.sort((a, b) => {
        if (a[property] === null) return 1;
        if (b[property] === null) return -1;
        return a[property] - b[property];
      });
    } else {
      lines.value.sort((a, b) => {
        if (a[property] === null) return 1;
        if (b[property] === null) return -1;
        return b[property] - a[property];
      });
    }
  }

  if (columnConfig.type === ColsTypeEnum.date) {
    if (order === SortOrder.ASC) {
      lines.value.sort((a, b) => {
        if (a[property] === null) return 1;
        if (b[property] === null) return -1;
        return new Date(a[property]).getTime() - new Date(b[property]).getTime();
      });
    } else {
      lines.value.sort((a, b) => {
        if (a[property] === null) return 1;
        if (b[property] === null) return -1;
        return new Date(b[property]).getTime() - new Date(a[property]).getTime();
      });
    }
  }
}

function onPageSizeChange(newValue: number) {
  emit('pageSize', newValue);
}

function onPageChange(newValue: number) {
  currentPage.value = newValue;
  emit('page', newValue);
}
</script>

<template>
  <div class="table-container">
    <table :class="{ 'fixed-first-col': fixedFirstCol, 'fixed-last-col': fixedLastCol }">
      <tr>
        <TableHeader
          v-for="(config, index) in props.config"
          :key="index"
          :config="config"
          :width="config.widthPx ?? defaultColWidth"
          :reset-sort="sortedColum?.property !== config.property"
          @sort="sort(config, $event)"
        >
        </TableHeader>
      </tr>

      <tr v-for="(line, index) in lines" :key="index">
        <TableRow v-for="(config, index) in props.config" :key="index" :config="config" :line="line" :index="index">
          <template v-if="config.type === ColsTypeEnum.slot" #[config.property!]>
            <slot :name="config.property"></slot>
          </template>
        </TableRow>
      </tr>
    </table>

    <div class="table-bottom" v-if="addPagination">
      <p class="showing">
        {{ t('pagination.showing') }}
        <span>
          {{
            t('pagination.pages', {
              first: currentPage * lines.length - (lines.length - 1),
              last: currentPage * lines.length,
              all: props.totalCount,
            })
          }}
        </span>
      </p>

      <select
        :value="lines.length"
        class="per-page-selected"
        @change="onPageSizeChange(+($event.target as HTMLSelectElement).value)"
      >
        <option v-for="(page, index) in possiblePageLength" :key="index" :value="page">
          {{ t('pagination.itemsPerPages', { page: page }) }}
        </option>
      </select>

      <Paginate :pages-count="pagesCount" :page="forceNavigationPage" @page-changed="onPageChange" />
    </div>
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

    &.fixed-first-col {
      td:first-child,
      th:first-child {
        position: sticky;
        left: 0;
        filter: drop-shadow(5px 5px 5px rgba(0, 0, 0, 0.1));
      }

      th:first-child {
        background: var(--table-header);
        z-index: 1;
      }

      tr:nth-child(odd) td:first-child {
        background: var(--table-even);
      }

      tr:nth-child(even) td:first-child {
        background: var(--background-secondary);
      }
    }

    &.fixed-last-col {
      td:last-child,
      th:last-child {
        position: sticky;
        right: 0;
        filter: drop-shadow(-5px 5px 5px rgba(0, 0, 0, 0.1));
      }

      th:last-child {
        background: var(--table-header);
        z-index: 1;
      }

      tr:nth-child(odd) td:last-child {
        background: var(--table-even);
      }

      tr:nth-child(even) td:last-child {
        background: var(--background-secondary);
      }
    }
  }

  .table-bottom {
    display: flex;
    align-items: center;
    align-self: flex-end;
    gap: 20px;

    .showing span {
      font-weight: bold;
    }

    .per-page-selected {
      display: flex;
      align-items: center;
      padding: 5px;

      font-size: 14px;
      cursor: pointer;
    }
  }
}

::-webkit-scrollbar-thumb {
  background: var(--table-scrollbar);
}
</style>
