<script setup lang="ts">
import NrtSelect from '../shared/NrtSelect.vue';
import { inject, ref } from 'vue';
import type { DropdownResult, SearchQuery } from '../../models/search-result.model.ts';
import { firstValueFrom } from 'rxjs';
import type ApiClient from '../../services/api.service.ts';
import { useI18n } from 'vue-i18n';
import { formatLabel } from '../../helpers/bold-text.helper.ts';

const { t } = useI18n();
const apiClient = inject<ApiClient>('apiClient') as ApiClient;

const currentSearchValue = ref<string>('');
const dropdownResult = ref<DropdownResult[]>([]);
const forceCloseDropdown = ref<boolean>(false);
const searchQuery = ref<SearchQuery>({} satisfies SearchQuery);

function selectItem(searchItem: DropdownResult) {
  dropdownResult.value = [];

  searchQuery.value = {
    ...searchQuery.value,
    propertyName: searchItem.source,
    propertyValue: searchItem.label,
    pageSize: 10,
    page: 1,
  } satisfies SearchQuery;
}

async function searchDropdown(searchTerm: string) {
  if (searchTerm?.length < 2) return;
  dropdownResult.value = (await firstValueFrom(apiClient.searchCOByMultipleFields(searchTerm))).data;
}
</script>

<template>
  <div class="select-demo">
    <h3>Simple select with static options</h3>
    <div class="select">
      <NrtSelect
        :options="[{ toto: 'gilles' }, { toto: 'andre' }, { toto: 'fatoumata' }, { toto: 'eloi' }]"
        :label-property="'toto'"
        @select="e => console.log('parent select :', e)"
      >
      </NrtSelect>
    </div>

    <h3>Complex select with backend search and custom template</h3>
    <div class="select">
      <NrtSelect
        v-model="currentSearchValue"
        :options="dropdownResult"
        :label-property="'label'"
        :close-dropdown="forceCloseDropdown"
        :local-search="false"
        :placeholder="t('app-home.criterias')"
        :use-custom-dropdown-template="true"
        @select="selectItem"
        @search="searchDropdown($event)"
      >
        <template #dropdown-option="{ option }">
          <div class="dropdown-option">
            <p v-html="formatLabel(option['label'], currentSearchValue)"></p>
            <span>{{ t('source.' + option['source']) }}</span>
          </div>
        </template>
      </NrtSelect>
    </div>
  </div>
</template>

<style scoped lang="scss">
.select-demo {
  display: flex;
  flex-direction: column;
  gap: 10px;

  h3 {
    margin-top: 10px;
    font-size: 20px;
  }

  .select {
    height: 35px;
  }

  .dropdown-option {
    display: flex;
    flex: 1;

    p {
      margin-right: auto;
    }
  }
}
</style>
