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
                <a :href="product.alkoLink" target="_blank" rel="noopener noreferrer">
                  {{ product.name }}
                </a>
                {{ product.alcoholPercentage }} - {{ product.price }}
                <input type="checkbox" :value="product" v-model="selected">
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
import { defineComponent, ref } from 'vue'
import { Product, Drinker, Gender, BacRequestDto, BacRepresentation } from '@/alkkis.types'
import Logo from "@/components/Logo.vue"
import "@/assets/styles/main.css"

export default defineComponent({
  name: 'App',
  components: {
    Logo
  },
  setup() {
    let products = ref<Product[]>([])
    let selected = ref<Product[]>([])
    let drinker = ref({
      gender: Gender.Male,
      weight: 80,
      time: 1
    } as Drinker)
    let result = ref('')
    let searchText = ref('')

    async function search() {
      products.value = await fetch(`http://localhost:3000/api/v1/search?name=${searchText.value}`)
      .then(res => res.json())
    }

    async function calculateBAC() {
      const data: BacRequestDto = {
        products: selected.value,
        drinker: drinker.value
      }

      const representation: BacRepresentation = await fetch(`http://localhost:3000/api/v1/bac`, {
        method: 'POST',
        body: JSON.stringify(data),
        headers: {
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        }
      })
      .then(res => res.json())
      result.value = representation.text
    }

    return {
      calculateBAC,
      result,
      searchText,
      search,
      products,
      selected,
      drinker,
      Gender
    }
  }
})
</script>
