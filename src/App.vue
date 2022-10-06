<template>
  <header class="header">
    <h2 class="header__title">Документы</h2>
    <div class="header__buttons">
      <ButtonFlag />

      <ButtonComponent v-for="button in buttons" :title="button.title" :key="button.id" />
    </div>
  </header>
</template>

<script setup>
import ButtonFlag from './components/ButtonFlag.vue';
import ButtonComponent from './components/ButtonComponent.vue';

const buttons = [{ id: 1, title: 'Новый тип' }, { id: 2, title: 'Новый документ' },]
</script>
