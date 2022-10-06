<template>
  <header class="header">
    <h2 class="header__title">Документы</h2>
    <div class="header__buttons">
      <ButtonFlag/>

      <ButtonComponent v-for="button in buttons" :title="button.title" :key="button.title"/>
    </div>
  </header>
  <div class="search_block">
    <InputSearch v-model="searchKey" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ButtonComponent from './components/ButtonComponent.vue';
import ButtonFlag from './components/ButtonFlag.vue';
import InputSearch from './components/InputSearch.vue'

const searchKey = ref('')
const buttons = [{ id: 1, title: 'Новый тип' }, { id: 2, title: 'Новый документ' },]
</script>
