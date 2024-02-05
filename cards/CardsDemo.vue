<script setup lang="ts">
import { onBeforeMount, ref } from 'vue';
import Card from '../shared/Card.vue';

const apparitionSpeed = 0.03;

interface CardItem {
  id: number;
  cardTitle: string;
  cardManagerName: string;
  cardPhone: string;
  cardAddress: string;
}

const cardItems = ref<CardItem[]>([]);

onBeforeMount(() => {
  setTimeout(() => {
    cardItems.value = [
      ...Array(10).fill({
        id: 0,
        cardTitle: 'Network Manager',
        cardManagerName: 'Gilles Drouin',
        cardPhone: '(514) 870-7772',
        cardAddress: '671 rue de Lagauchetière Ouest1',
      }),
      ...Array(10).fill({
        id: 0,
        cardTitle: 'Roles Manager',
        cardManagerName: 'Eloi Bellet',
        cardPhone: '(514) 870-7772',
        cardAddress:
          '671 rue de Lagauchetière Ouest1 671 rue de Lagauchetière Ouest1671 rue de Lagauchetière Ouest1671 rue de Lagauchetière Ouest1671 rue de Lagauchetière Ouest1',
      }),
    ];
  }, 2000);
});
</script>

<template>
  <!--  skeleton-->
  <div class="card-container" v-if="!cardItems.length">
    <Card
      class="card"
      v-for="index in 20"
      :key="index"
      :skeleton="true"
      :style="{ 'animation-delay': `${apparitionSpeed * index}s` }"
    >
    </Card>
  </div>

  <div class="card-container">
    <Card
      class="card"
      v-for="(cardItem, index) in cardItems"
      :key="cardItem.id"
      :style="{ 'animation-delay': `${apparitionSpeed * index}s` }"
      :title="cardItem.cardTitle"
    >
      <template #card-text>
        <div class="card-text-wrapper">
          <p>{{ cardItem.cardManagerName }}</p>
          <p>{{ cardItem.cardPhone }}</p>
          <p>{{ cardItem.cardAddress }}</p>
        </div>
      </template>
    </Card>
  </div>
</template>

<style scoped lang="scss">
.card-container {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;

  .card {
    animation-name: appear;
    opacity: 0;
    animation-duration: 1s;
    animation-fill-mode: forwards;

    .card-text-wrapper {
      display: flex;
      flex-direction: column;
      gap: 7px;
    }
  }
}

@keyframes appear {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
