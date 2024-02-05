<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import { BehaviorSubject, debounceTime } from 'rxjs';

const props = withDefaults(
  defineProps<{
    pagesCount: number;
    page: number;
  }>(),
  {
    page: 1,
  }
);

const searchSubject = new BehaviorSubject<number>(1);

onMounted(() => {
  searchSubject.pipe(debounceTime(1000)).subscribe(result => {
    navigate(result);
  });
});

const currentPage = ref<number>(1);

const emit = defineEmits(['page-changed']);

watch(
  () => props.page,
  () => {
    currentPage.value = props.page;
  }
);

function navigate(newPage: number) {
  if (newPage === currentPage.value) return;
  currentPage.value = newPage;
  if (!newPage) currentPage.value = 1;
  if (newPage < 1) {
    currentPage.value = 1;
    return;
  }
  if (newPage > props.pagesCount) {
    currentPage.value = props.pagesCount;
    return;
  }
  emit('page-changed', currentPage.value);
}
</script>

<template>
  <div class="pagination">
    <a @click="navigate(currentPage - 1)" :class="{ disabled: currentPage === 1 }">
      <i class="fa fa-angle-left"></i>
    </a>
    <input
      type="number"
      :value="currentPage"
      min="1"
      :max="pagesCount"
      @input="newPage => searchSubject.next(+(newPage.target as HTMLInputElement).value)"
    />
    <p>/ {{ pagesCount }}</p>
    <a @click="navigate(currentPage + 1)" :class="{ disabled: currentPage === pagesCount }">
      <i class="fa fa-angle-right"></i>
    </a>
  </div>
</template>

<style scoped>
.pagination {
  display: flex;
  align-items: center;
  gap: 7px;
  height: 30px;

  a {
    cursor: pointer;
    font-size: 20px;

    &.disabled {
      color: var(--disabled);
    }
  }

  input {
    padding: 5px;
    width: 40px;
    border: 1px solid var(--select-border);
    border-radius: 5px;
    font-size: 14px;
    align-self: normal;
  }
}
</style>
