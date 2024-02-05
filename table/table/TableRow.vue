<script setup lang="ts">
import { type ColumnConfig } from '../../models/table-data.model';
import { ColsTypeEnum } from '../../enums/cols-type.enum.ts';
import { formatDate } from '../../helpers/format-date.helper.ts';

const props = withDefaults(
  defineProps<{
    config: ColumnConfig;
    line: any;
    index: number;
  }>(),
  {}
);

function formatDatailUrl() {
  const key = props.config.detailUrl?.substring(
    props.config.detailUrl.indexOf('{') + 1,
    props.config.detailUrl.lastIndexOf('}')
  )!;
  return props.config.detailUrl?.replace(`{${key}}`, props.line[key])!;
}
</script>

<template>
  <td>
    <div class="td-container">
      <p v-if="[ColsTypeEnum.text, ColsTypeEnum.decimal].includes(props.config?.type!)">
        {{ props.line[props.config.property!] }}
      </p>
      <p v-if="props.config.type === ColsTypeEnum.date">{{ formatDate(props.line[props.config.property!]) }}</p>
      <router-link target="_blank" v-if="props.config.type === ColsTypeEnum.detail" :to="formatDatailUrl()">
        Detail
      </router-link>
      <i v-if="props.config.type === ColsTypeEnum.icon" class="fa-solid" :class="props.config.iconClass"></i>
      <slot v-if="props.config.type === ColsTypeEnum.slot" :name="config.property"></slot>
    </div>
  </td>
</template>

<style scoped lang="scss">
.td-container {
  p {
    white-space: nowrap;
  }

  a {
    color: var(--text-primary);
    text-decoration: underline;
    font-weight: bold;
  }
}
</style>
