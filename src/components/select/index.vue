<template>
  <div id="select">
    <div id="input">
      <input :class="inputClass"
             :placeholder="inputPlaceholder"
             v-model="search"
             @click="showDistricts"
      >
      <button v-if="search.length" @click="clearSearch"/>
      <p v-if="$v.selected_districts.$error" class="error">Выберите хотя бы один район</p>
    </div>
    <div v-if="districts_show" class="districts">
      <div v-if="selected_districts.length" class="districts-selected">
        <label v-for="district in selected_districts"
               :key="district"
               @click="removeDistrict(district)"
        >
          {{ district }}
        </label>
      </div>
      <div class="districts-list">
        <div class="district"
             v-for="(district, index) in filterDistricts"
             :key="index"
        >
          <input :id="'checkbox' + index"
                 type="checkbox"
                 :disabled="isDisabled(district)"
                 :value="district"
                 v-model="selected_districts"
          >
          <label :for="'checkbox' + index" v-html="matchDistrict(district)"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { required } from "vuelidate/lib/validators"
export default {
  name: "App",
  data: () => ({
    districts: [],
    disabled_districts: [],
    selected_districts: [],
    search: "",
    districts_show: false
  }),
  validations: () => ({
    selected_districts: { required }
  }),
  created() { this.getDistricts() },
  computed: {
    inputPlaceholder() {
      return this.districts_show ? "Поиск по районам" : "Не выбрано"
    },
    inputClass() {
      return this.districts_show ? "active" : "disabled"
    },
    filterDistricts() {
      if (this.search.length) {
        return this.districts.filter(district => {
          return district.toLowerCase().includes(this.search.toLowerCase())
        })
      } else { return this.districts }
    }
  },
  methods: {
    getDistricts() {
      this.districts = require("../../../moscow_districts.json")
      this.disabled_districts = require("../../../disabled_districts.json")
    },
    showDistricts() {
      this.districts_show = true
    },
    isDisabled(district) {
      return this.disabled_districts.includes(district)
    },
    matchDistrict(district) {
      let reggie = new RegExp(this.search, "ig")
      if (this.search) {
        return "<div class='found'>" + district.replace(reggie, "<b>" + this.search + "</b>") + "</div>"
      } else { return district }
    },
    removeDistrict(district) {
      this.selected_districts = this.selected_districts.filter(item => item !== district)
    },
    clearSearch() { this.search = "" }
  }
}
</script>

<style lang="sass">@import "style"</style>
