<script setup lang="ts">
import { inject, onMounted, ref } from 'vue';
import { firstValueFrom } from 'rxjs';
import { type ColumnConfig } from '../../models/table-data.model.ts';
import type ApiClient from '../../services/api.service.ts';
import { ColsTypeEnum } from '../../enums/cols-type.enum.ts';
import AppTable from '../table/AppTable.vue';
import { SortOrder } from '../../enums/sort-order.enum.ts';

export interface TableDemoDto {
  id: number;
  firstName: string;
  lastName: string;
  userName: string;
  birthDate: Date;
  age: number;
  address: string;
  email: string;
  phoneNumber: string;
}

export interface TableDemoQuery {
  page?: number;
  pageSize?: number;
  sortColumn?: string;
  sortOrder?: SortOrder;
}

export interface TableDemoResponse {
  data: TableDemoDto[];
  totalCount: number;
}

const totalCount = ref<number>(10);
const lines = ref<TableDemoDto[]>([]);
const config = ref<ColumnConfig[]>([]);
const searchQuery = ref<TableDemoQuery>({ page: 1, pageSize: 10 });

const apiClient = inject<ApiClient>('apiClient') as ApiClient;

onMounted(async () => {
  config.value = [
    { property: 'id', name: 'toto', type: ColsTypeEnum.decimal, widthPx: 40 },
    { property: 'firstName', name: 'firstName', type: ColsTypeEnum.text },
    { property: 'lastName', name: 'lastName', type: ColsTypeEnum.text },
    { property: 'userName', name: 'userName', type: ColsTypeEnum.text },
    { property: 'birthDate', name: 'birthDate', type: ColsTypeEnum.date },
    { property: 'age', name: 'age', type: ColsTypeEnum.decimal },
    { property: 'address', name: 'address', type: ColsTypeEnum.text },
    { property: 'email', name: 'email', type: ColsTypeEnum.text },
    { property: 'phoneNumber', name: 'phoneNumber', type: ColsTypeEnum.text },
    { type: ColsTypeEnum.detail, detailUrl: '/detail/{id}' },
    { type: ColsTypeEnum.icon, iconClass: 'fa-star' },
    { name: 'test', type: ColsTypeEnum.icon, iconClass: 'fa-user' },
    { property: 'slot-content', name: 'slot-content', type: ColsTypeEnum.slot },
    { property: 'slot-content2', name: 'slot-content2', type: ColsTypeEnum.slot },
  ] as ColumnConfig[];

  await refresh();
});

async function refresh() {
  const { totalCount: localCount, data } = (await firstValueFrom(apiClient.tableDemoData(searchQuery.value))).data;
  lines.value = data;
  totalCount.value = localCount;
}

async function onPageSizeChange(newPageSize: number) {
  searchQuery.value = { ...searchQuery.value, pageSize: newPageSize };
  await refresh();
}

async function onPageChange(newPage: number) {
  searchQuery.value = { ...searchQuery.value, page: newPage };
  await refresh();
}

async function onSort({ columnConfig, order }: { columnConfig: ColumnConfig; order: SortOrder }) {
  searchQuery.value = { ...searchQuery.value, sortColumn: columnConfig.property, sortOrder: order };
  if (order === SortOrder.NOSORT) {
    delete searchQuery.value.sortOrder;
    delete searchQuery.value.sortColumn;
  }
  await refresh();
}
</script>

<template>
  <AppTable
    :total-count="totalCount"
    :fixed-first-col="true"
    :fixed-last-col="true"
    :config="config"
    :data="lines"
    @page-size="onPageSizeChange"
    @page="onPageChange"
    @sort="onSort"
  >
    <template #slot-content>
      <div class="img-container">
        <img src="https://upload.wikimedia.org/wikipedia/commons/b/b6/Image_created_with_a_mobile_phone.png" alt="" />
      </div>
    </template>
    <template #slot-content2>
      <p>slot-content2</p>
    </template>
  </AppTable>
</template>

<style scoped lang="scss">
.img-container {
  display: flex;
  align-items: center;

  img {
    height: 20px;
  }
}
</style>
