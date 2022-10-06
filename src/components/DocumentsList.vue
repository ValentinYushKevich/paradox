<template>
  <div style="max-width: 1190px">
    <div v-if="isSearch && searchResultComputed.length < 1" class="search-not-found">
      Ничего не найдено. Попробуйте поменять параметры поиска
    </div>
    <template v-else>
      <draggable :dragoverBubble="true" :group="{name: 'category', pull: 'clone', put: false}"
        @start="state.dragTopLevel = true" @end="state.dragTopLevel = false" class="doc_list-box"
        v-model="state.categoryData" handle=".doc-list-item__btn_handle" item-key="id">
        <template #item="{element}">
          <div class="doc_list-box__item"
            :class="{'doc_list-box__item_collapsed': element.showItems, 'd-none' : !inSearch(element)}">
            <DocumentItem :initData="element" :isCategory="true"
              @toggle-collapse="element.showItems = !element.showItems" />

            <draggable style="position: relative"
              :style="{maxHeight: element.showItems ? (element.items.length * 44) + 'px' : 0}"
              @change="element.showItems = true" handle=".doc-list-item__btn_handle" class="subcategory-list"
              v-model="element.items" item-key="id" :group="{name: 'subcat', put: !state.dragTopLevel}">
              <template #item="{ element: content, index: contentIndex }">
                <DocumentItem :class="{'d-none' : !inSearch(content)}" :initData="content"
                  :isHide="!element.showItems" />
              </template>
            </draggable>

          </div>
        </template>
      </draggable>
      <draggable style="margin-top: 15px" handle=".doc-list-item__btn_handle" class="subcategory-list-free"
        v-model="state.otherCategories.items" item-key="id" :group="{name: 'subcat', put: !state.dragTopLevel}">
        <template #item="{element}">
          <DocumentItem :initData="element" :class="{'d-none' : !inSearch(element)}" />
        </template>
      </draggable>
    </template>
  </div>
</template>

<script setup>
import { defineProps, reactive, computed } from 'vue'
import draggable from 'vuedraggable'
import DocumentItem from './DocumentItem.vue'

const exampleCategories = [
  {
    id: 'required-for-all',
    title: 'Обязательные для всех',
    description: 'Документы, обязательные для всех сотрудников без исключения',
    dots: ['FF238D', 'FFB800', 'FF8D23'],
    isCategory: true
  },
  {
    id: 'passport',
    title: 'Паспорт',
    dots: ['00C2FF'],
    tags: [{ type: 'danger', text: 'Обязательный' }, { type: 'default', text: 'Для всех' }],
    parentId: 'required-for-all'
  },
  {
    id: 'inn',
    title: 'ИНН',
    tags: [{ type: 'danger', text: 'Обязательный' }, { type: 'default', text: 'Для всех' }],
    parentId: 'required-for-all'
  },
  {
    id: 'required-for-jobs',
    title: 'Обязательные для трудоустройства',
    description: 'Документы, без которых невозможно трудоустройство человека на какую бы то ни было должность в компании вне зависимости от гражданства',
    isCategory: true
  },
  {
    id: 'special',
    title: 'Специальные',
    isCategory: true
  },
  {
    id: 'candidate-test',
    title: 'Тестовое задание кандидата',
    description: 'Россия, Белоруссия, Украина, администратор филиала, повар-сушист, повар-пиццмейкер, повар горячего цеха'
  },
  {
    id: 'labor-contract',
    title: 'Трудовой договор',
  },
  {
    id: 'med-book',
    title: 'Мед. книжка',
    dots: ['0066FF', '8E9CBB'],
  },
]
const categoryData = [
  {
    id: 'required-for-all',
    title: 'Обязательные для всех',
    description: 'Документы, обязательные для всех сотрудников без исключения',
    dots: ['FF238D', 'FFB800', 'FF8D23'],
    showItems: true,
    items: [
      {
        id: 'passport',
        title: 'Паспорт',
        dots: ['00C2FF'],
        tags: [{ type: 'danger', text: 'Обязательный' }, { type: 'default', text: 'Для всех' }],
      },
      {
        id: 'inn',
        title: 'ИНН',
        tags: [{ type: 'danger', text: 'Обязательный' }, { type: 'default', text: 'Для всех' }],
      },
    ]
  },
  {
    id: 'required-for-jobs',
    title: 'Обязательные для трудоустройства',
    description: 'Документы, без которых невозможно трудоустройство человека на какую бы то ни было должность в компании вне зависимости от гражданства',
    showItems: false,
    items: []
  },
  {
    id: 'special',
    title: 'Специальные',
    showItems: false,
    items: []
  },

]
const otherCategories = {
  items: [
    {
      id: 'candidate-test',
      title: 'Тестовое задание кандидата',
      description: 'Россия, Белоруссия, Украина, администратор филиала, повар-сушист, повар-пиццмейкер, повар горячего цеха'
    },
    {
      id: 'labor-contract',
      title: 'Трудовой договор',
    },
    {
      id: 'med-book',
      title: 'Мед. книжка',
      dots: ['0066FF', '8E9CBB'],
    },
  ]

}
const props = defineProps({
  searchKey: {
    type: String
  }
})

const state = reactive({
  listData: exampleCategories,
  categoryData: categoryData,
  otherCategories: otherCategories,
  dragTopLevel: false
})

const isSearch = computed(() => props.searchKey.length > 0)

function searchResult() {
  if (!isSearch.value) return null

  let results = []

  state.categoryData.forEach(category => {
    if (category.title.toLowerCase().includes(props.searchKey.toLowerCase())) results.push(category.id)

    category.items.length && category.items.forEach(subcat => {
      if (subcat.title.toLowerCase().includes(props.searchKey.toLowerCase())) {
        if (!results.includes(category.id)) {
          results.push(category.id)
          category.showItems = true
        }
        results.push(subcat.id)
      }
    })
  })

  state.otherCategories.items.forEach((otherCategories)=> {
    if (otherCategories.title.toLowerCase().includes(props.searchKey.toLowerCase())) results.push(otherCategories.id)
  })

  return results;
}

const searchResultComputed = computed(() => searchResult())

function inSearch(row) {
  return isSearch.value ? searchResultComputed.value.includes(row.id) : true
}
</script>
