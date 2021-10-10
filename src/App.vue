<template>
  <div class="container">
    <div>
      <BacParams @drinkerUpdated="updateDrinkerParams" />
      <ProductSearch @productListUpdated="updateProductList" />
      <button @click="calculateBAC">Laske</button>
      <p v-if="result">{{ result }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { Product } from "@/domain/product.type"
import { Drinker } from "@/domain/drinker.type"
import { BacData } from "@/domain/bac-data.type"
import ProductSearch from "@/components/ProductSearch.vue";
import BacParams from "@/components/BacParams.vue";

export default defineComponent({
  name: 'App',
  components: {
    BacParams,
    ProductSearch
  },
  setup() {
    let products: Product[] = []
    let drinker: Drinker
    let result: ?string

    function updateProductList(list: Product[]) {
      products = list
    }

    function updateDrinkerParams(data: Drinker) {
      drinker = data
    }

    async function calculateBAC() {
      const data: BacData = {
        products: products,
        drinker: drinker
      }

      let bac = await fetch(`/api/bac`, {
        method: 'POST',
        body: JSON.stringify(data),
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        }
      })
      .then(res => res.json())
      console.log(bac)
    }

    return {
      updateDrinkerParams,
      updateProductList,
      calculateBAC,
      result
    }
  }
})
</script>
