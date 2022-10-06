<template>
  <div class="doc-list-item"
    :class="{'doc-list-item_category' : props.isCategory, 'doc-list-item_collapsed' : props.isHide}">
    <CollapseButton v-if="props.isCategory" @toggleCollapse='emit("toggle-collapse")'/>
    <div class="doc-list-item__title">
      {{props.initData.title}}
    </div>
    <div class="doc-list-item__dots" v-if="props.initData.hasOwnProperty('dots') && props.initData.dots.length > 0">
      <span v-for="dotColor in props.initData.dots" class="doc-list-item__dot"
        :style="{'--app-doc-list-item__dot-bg' : `#${dotColor}`}">
      </span>
    </div>
    <div class="doc-list-item__tags" v-if="props.initData.hasOwnProperty('tags') && props.initData.tags.length > 0">
      <span v-for="tag in props.initData.tags" class="doc-list-item__tag" :class="[`doc-list-item__tag_${tag.type}`]">
        {{tag.text}}
      </span>
    </div>
    <div class="doc-list-item__description"
      v-if="props.initData.hasOwnProperty('description') && props.initData.description.length > 0">
      {{props.initData.description}}
    </div>
    <ButtonGroup/>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'
import CollapseButton from './CollapseButton.vue';
import ButtonGroup from './ButtonGroup.vue';

const emit = defineEmits(['toggle-collapse'])

const props = defineProps({
  initData: {
    type: Object,
  },
  isCategory: {
    type: Boolean,
    default: false
  },
  isHide: {
    type: Boolean,
    default: false
  }
})
</script>