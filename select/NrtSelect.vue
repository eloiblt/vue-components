<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import { BehaviorSubject, debounceTime, filter } from 'rxjs';
import { formatLabel } from '../../helpers/bold-text.helper.ts';

const currentSearchValue = defineModel<string>();

const loading = ref<boolean>(false);
const searchSubject = new BehaviorSubject<string>('');
const dropdownOptions = ref<any[]>([]);

const props = withDefaults(
  defineProps<{
    options: any[];
    // the property in option we want to print
    labelProperty: string;
    // true : options must be a static string[], never empty
    // false : options must be an any[] with a labelProperty set
    localSearch?: boolean;
    useCustomDropdownTemplate?: boolean;
    placeholder?: string;
    closeDropdown?: boolean;
  }>(),
  {
    localSearch: true,
    useCustomDropdownTemplate: false,
  }
);

/**
 * search : only required is localSearch = false, emitted when a value is ready to be searched
 * select : emitted whenever an option is selected
 */
const emit = defineEmits(['search', 'select']);

watch(
  () => props.options,
  () => {
    if (!props.options?.length || props.localSearch) return;
    dropdownOptions.value = [...props.options];
    loading.value = false;
  }
);

watch(
  () => props.closeDropdown,
  () => {
    dropdownOptions.value = [];
    loading.value = false;
  }
);

onMounted(async () => {
  searchSubject
    .pipe(
      debounceTime(400),
      filter(value => {
        const validSearchValue = !!value && value.length > 1;
        if (!validSearchValue) dropdownOptions.value = [];
        return validSearchValue;
      })
    )
    .subscribe(searchValue => {
      loading.value = true;
      emit('search', searchValue);
    });
});

function input() {
  if (!currentSearchValue.value?.trim().length) {
    dropdownOptions.value = [];
    return;
  }

  if (!props.localSearch) {
    searchSubject.next(currentSearchValue.value);
    return;
  }

  dropdownOptions.value = [...props.options].filter(o => o[props.labelProperty!].includes(currentSearchValue.value));
}

function select(option: any) {
  currentSearchValue.value = option[props.labelProperty];
  dropdownOptions.value = [];
  emit('select', option);
}
</script>

<template>
  <div class="app-select">
    <div class="input-container">
      <input type="text" v-model="currentSearchValue" :placeholder="props.placeholder" @input="input()" />
      <div class="search-icon">
        <div v-if="!loading"><i class="fa-solid fa-search"></i></div>
        <div v-if="loading" class="pulse"></div>
      </div>
    </div>
    <div
      class="dropdown-results"
      v-click-outside="() => (dropdownOptions = [])"
      :class="{ show: dropdownOptions.length }"
    >
      <div class="dropdown-option-wrapper" v-for="option in dropdownOptions" :key="option.id" @click="select(option)">
        <slot v-if="props.useCustomDropdownTemplate" name="dropdown-option" :option="option"></slot>
        <p
          v-if="!props.useCustomDropdownTemplate"
          v-html="formatLabel(option[props.labelProperty!], currentSearchValue)"
        ></p>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.app-select {
  display: flex;
  flex: 1;
  flex-direction: column;
  position: relative;
  height: 100%;

  .input-container {
    position: relative;
    display: flex;
    flex: 1;

    input {
      border: 1px solid black;
      border-radius: 6px;
      flex: 1;
      padding: 5px 30px 5px 5px;
      outline: none;

      &::placeholder {
        color: var(--disabled);
      }
    }

    .search-icon {
      position: absolute;
      right: 5px;
      top: 8px;
      width: 20px;
      height: 20px;

      svg {
        transform: scale(1.1) translateY(1px);
      }

      .pulse {
        border-radius: 50%;
        animation: pulse 0.8s infinite;
        height: 20px;
        width: 20px;
        background-color: rgb(var(--pulse-color));
      }

      @keyframes pulse {
        0% {
          transform: scale(0.9);
          box-shadow: 0 0 0 0 rgba(var(--pulse-color), 0.7);
        }

        70% {
          transform: scale(1.1);
          box-shadow: 0 0 0 10px rgba(var(--pulse-color), 0);
        }

        100% {
          transform: scale(0.9);
          box-shadow: 0 0 0 0 rgba(var(--pulse-color), 0);
        }
      }
    }
  }

  .dropdown-results {
    background: white;
    position: absolute;
    top: 40px;
    left: 0;
    right: 0;
    border-radius: 5px;
    box-shadow: 0 0 10px -7px var(--card-shadow);
    display: flex;
    flex-direction: column;
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.2s ease-out;
    z-index: 2;

    .dropdown-option-wrapper {
      cursor: pointer;
      padding: 5px 10px;
      border-radius: 5px;
      display: flex;

      p {
        margin-right: auto;
      }

      &:hover {
        background: var(--background-primary);
      }
    }

    &.show {
      padding: 5px;
      max-height: 500px;
    }
  }
}
</style>
