<template>
  <section>
    <div class="box">
      <h1 class="subtitle">Juomarin parametrit</h1>
      <div class="block">
        <input type="radio" id="female" v-model="drinker.gender" @change="genderChange(1)">
        <label for="female">Nainen</label>
        <input type="radio" id="male" v-model="drinker.gender" @change="genderChange(2)">
        <label for="male">Mies</label>
      </div>
      <label for="weight">Paino (kg):</label>
      <input class="stacked" type="number" id="weight" v-model.number="drinker.weight" @change="onDrinkerChange">
      <label for="time">Juoma-aika tunneissa:</label>
      <input class="stacked" type="number" id="time" v-model.number="drinker.time" @change="onDrinkerChange">
    </div>
  </section>
</template>

<script lang="ts">
import {Drinker, Gender} from "@/domain/drinker.type"
import {defineComponent} from "vue"

export default defineComponent({
  name : "BacParams",
  emits: ['drinkerUpdated'],
  setup(props, { emit }) {
    let drinker: Drinker = {
      gender: Gender.Male,
      weight: 80,
      time: 1
    }

    function onDrinkerChange() {
      emit('drinkerUpdated', drinker)
    }

    function genderChange(value: number) {
      drinker.gender = value === 1 ? Gender.Female : Gender.Male
      onDrinkerChange()
    }

    return {
      drinker,
      onDrinkerChange,
      genderChange
    }
  }
})
</script>