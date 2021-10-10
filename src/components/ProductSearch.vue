<template>
  <section>
    <div class="box">
      <h1 class="subtitle">Tuotehaku</h1>
      <label class="inline" for="search-text">Hakusana</label>
      <input class="inline" type="text" id="search-text" v-model="searchText">
      <button class="grey" @click="search">Hae</button>
      <div v-if="products.length > 0">
        <ul v-for="product in products" :key="product.num">
          <li>
            <a :href="getAlkoLink(product.num)" target="_blank" rel="noopener noreferrer">
              {{ product.name }}
            </a>
            {{ product.alkopros }} - {{ product.price }}
            <input type="checkbox" :selected="selected.includes(product)" @change="updateSelected(product)">
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<script lang="ts">
import { Product } from "@/domain/product.type"
import { defineComponent } from "vue"
export default defineComponent({
  name : "ProductSearch",
  emits : ['productListUpdated'],
  setup(props, { emit }) {
    let products: Product[] = []
    let selected: Product[] = []
    let searchText = ''

    async function search() {
      products = await fetch(`/api/search?name=${searchText}`)
      .then(res => res.json())
    }

    function updateSelected(product: Product) {
      if (selected.includes(product)) selected.splice(selected.indexOf(product), 1)
      else selected.push(product)
      emit('productListUpdated', selected)
    }

    function getAlkoLink(productNumber: number): string {
      return `https://www.alko.fi/tuotteet/${productNumber}/`
    }

    return {
      products,
      selected,
      searchText,
      search,
      updateSelected,
      getAlkoLink
    }
  }
})
</script>