<template>
  <div>
    <h1>Tuotehaku</h1>
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
</template>

<script lang="ts">
import { Product } from "@/domain/product.type"
export default {
  name : "ProductSearch",
  data : () => {
    return {
      products: [] as Product[],
      selected: [] as Product[]
    }
  }
}
</script>