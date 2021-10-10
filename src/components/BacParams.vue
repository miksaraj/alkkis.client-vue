<template>
  <section>
    <div class="box">
      <h1 class="subtitle">Juomarin parametrit</h1>
      <div class="block">
        <input type="radio" id="female" value="1" v-model="drinker.gender" @change="onDrinkerChange">
        <label for="female">Nainen</label>
        <input type="radio" id="male" value="2" v-model="drinker.gender" @change="onDrinkerChange">
      </div>
      <label for="weight">Paino (kg):</label>
      <input type="number" id="weight" v-model.number="drinker.weight" @change="onDrinkerChange">
      <label for="time">Juoma-aika tunneissa:</label>
      <input type="number" id="time" v-model.number="drinker.time" @change="onDrinkerChange">
    </div>
  </section>
</template>

<script lang="ts">
import { Drinker } from "@/domain/drinker.type"
import { defineComponent } from "vue"

export default defineComponent({
  name : "BacParams",
  emits: ['drinkerUpdated'],
  setup() {
    let drinker: Drinker

    function onDrinkerChange() {
      this.$emit('drinkerUpdated', drinker)
    }

    return {
      drinker,
      onDrinkerChange
    }
  }
})
</script>