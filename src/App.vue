<template>
  <div class="container">
    <div>
      <Logo />
      <section>
        <div class="box">
          <h1 class="subtitle">Juomarin parametrit</h1>
          <label class="inline" for="gender">Sukupuoli:</label>
          <select class="inline" id="gender" v-model="drinker.gender">
            <option disabled :value="undefined">Valitse...</option>
            <option :value="Gender.Female">Nainen</option>
            <option :value="Gender.Male">Mies</option>
          </select>
          <br>
          <label for="weight">Paino (kg):</label>
          <input class="stacked" type="number" id="weight" v-model.number="drinker.weight">
          <label for="time">Juoma-aika tunneissa:</label>
          <input class="stacked" type="number" id="time" v-model.number="drinker.time">
        </div>
      </section>
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
                <input type="checkbox" :checked="selected.includes(product)" @change="updateSelected(product)">
              </li>
            </ul>
          </div>
        </div>
      </section>
      <button class="green" @click="calculateBAC">Laske</button>
      <p v-if="result">{{ result }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { Product } from "@/domain/product.type"
import { Drinker, Gender } from "@/domain/drinker.type"
import { BacData } from "@/domain/bac-data.type"
import Logo from "@/components/Logo.vue"
import "@/assets/styles/main.css"

export default defineComponent({
  name: 'App',
  components: {
    Logo
  },
  setup() {
    let products: Product[] = []
    let selected: Product[] = []
    let drinker: Drinker = {
      gender: Gender.Male,
      weight: 80,
      time: 1
    }
    let result = ''
    let searchText = ''

    /**
     * Note: the url differs between backend implementations:
     * NestJS implementation: localhost:3000/v1/alko/search?...
     * Ts.ED implementation: localhost:8083/v1/alko/search?...
     * FoalTS implementation not functional as of yet
     *
     * They will be standardised once I get to it, but for
     * now the callable url depends on used backend version.
     */
    async function search() {
      products = await fetch(`http://localhost:3000/api/search?name=${searchText}`)
      .then(res => res.json())
    }

    function updateSelected(product: Product) {
      if (selected.includes(product)) selected.splice(selected.indexOf(product), 1)
      else selected.push(product)
    }

    function getAlkoLink(productNumber: number): string {
      return `https://www.alko.fi/tuotteet/${productNumber}/`
    }

    async function calculateBAC() {
      const data: BacData = {
        products: products,
        drinker: drinker
      }

      /**
       * Note: the url differs between backend implementations:
       * NestJS implementation: localhost:3000/v1/bac/
       * Ts.ED implementation: localhost:8083/v1/bac/
       * FoalTS implementation: localhost:3001/bac/
       *
       * They will be standardised once I get to it, but for
       * now the callable url depends on used backend version.
       */
      let bac = await fetch(`http://localhost:3000/api/bac`, {
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
      calculateBAC,
      result,
      searchText,
      search,
      updateSelected,
      getAlkoLink,
      products,
      selected,
      drinker,
      Gender
    }
  }
})
</script>
