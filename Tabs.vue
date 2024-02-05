<script setup lang="ts">
import { ref } from 'vue';
import { useI18n } from 'vue-i18n';

const props = withDefaults(
  defineProps<{
    tabs: string[];
  }>(),
  {}
);

const { t } = useI18n();
const currentTab = ref<string>(props.tabs[0]);

function changeTab(tab: string) {
  if (currentTab.value === tab) return;
  currentTab.value = tab;
}
</script>

<template>
  <div class="tabs-container left">
    <div class="tabs-items">
      <h3
        @click="changeTab(tab)"
        v-for="(tab, index) in props.tabs"
        :key="index"
        :class="{ active: currentTab === tab }"
      >
        {{ t(`detail.${tab}`) }}
      </h3>
    </div>

    <div class="tabs">
      <Transition name="fade">
        <div :key="currentTab" class="tab card">
          <slot :name="currentTab"></slot>
        </div>
      </Transition>
    </div>
  </div>
</template>

<style scoped lang="scss">
.tabs-container {
  display: flex;
  flex-direction: column;
  gap: 13px;

  .tabs-items {
    display: flex;
    align-items: center;
    gap: 20px;

    h3 {
      cursor: pointer;

      &.active {
        text-decoration-thickness: 2px;
        text-decoration-line: underline;
        text-underline-offset: 7px;
        text-decoration-color: var(--primary-color);
      }
    }
  }

  .tabs {
    position: relative;

    &:after {
      position: absolute;
      content: '';
      bottom: -20px;
      height: 20px;
      width: 1px;
    }
  }

  .tab {
    position: absolute;
    left: 0;
    right: 0;
    padding: 20px;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s linear;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
