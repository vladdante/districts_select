<template>
  <div id="select">
    <div id="input">
      <input :class="inputClass"
             :placeholder="inputPlaceholder"
             v-model="search"
             @click="showDistricts"
      >
      <button v-if="search.length" @click="clearSearch"/>
    </div>
    <p :class="{ 'error': v$.selected_districts.$error }">Выберите хотя бы один район</p>
    <div v-if="selected_districts.length" class="districts-selected">
      <label v-for="district in selected_districts"
             :key="district"
             @click="removeDistrict(district)"
      >
        {{ district }}
      </label>
    </div>
    <div v-if="districts_show" class="districts">
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
        <label :for="'checkbox' + index">{{ district }}</label>
      </div>
    </div>
  </div>
</template>

<script>
import useVuelidate from "@vuelidate/core"
import { required } from "@vuelidate/validators"
export default {
  setup: () => ({ v$: useVuelidate() }),
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
    removeDistrict(district) {
      this.selected_districts = this.selected_districts.filter(item => item !== district)
    },
    clearSearch() { this.search = "" }
  }
}
</script>

<style lang="sass">
$purple: #bc99de
$deep_purple: #a180c1
$grey: #b7b7b7
$radius: 5px
$border-width: 1px
$text-padding: 15px

#select
  width: 300px

  #input
    position: relative
    input
      border-radius: $radius
      width: available
      width: -webkit-fill-available
      width: -moz-available
      font-size: 1em
      border: solid 2px $grey
      -webkit-transition: 0.5s
      transition: 0.5s
      &::placeholder
        color: $grey
      &:hover
        border-color: $purple
        cursor: pointer
      &:focus
        border-color: $purple
        outline: none
        cursor: initial
      &.disabled
        background: url("../../assets/icons/sort-down.svg") no-repeat right 15px bottom 60%
        background-size: 9px
        padding: 15px 30px 15px 15px
      &.active
        border-color: $purple
        background: url("../../assets/icons/search.png") no-repeat left 17px bottom 50%
        background-size: 13px
        padding: 15px 30px 15px 42px
        border-bottom: solid 1px #e5e5e5
        border-bottom-right-radius: 0
        border-bottom-left-radius: 0
      &.error
        border-color: red
    button
      background: url("../../assets/icons/times.png") no-repeat center
      background-size: 10px
      height: 40px
      width: 40px
      position: absolute
      top: 5px
      right: 0
      border: none
      cursor: pointer

  .districts
    overflow: auto
    height: 145px
    border: solid 2px $purple
    border-top: none
    border-bottom-left-radius: $radius
    border-bottom-right-radius: $radius
    padding: 17px
    &::-webkit-scrollbar
      width: 11px
      margin-right: 3px
      &-thumb
        background-color: #cdc0d9
        border: solid 3px white
        border-radius: 5px
        &:hover
          cursor: pointer
    &-selected
      border-bottom: dashed 1px #e5e5e5
      border-right: solid 2px $purple
      border-left: solid 2px $purple
      padding: 15px
      label
        background: $purple url("../../assets/icons/times-white.png") no-repeat right 7px bottom 50%
        background-size: 7px
        color: white
        margin: 2px
        padding: 5px 20px 5px 5px
        font-size: 0.8em
        line-height: 2.4em
        border-radius: 3px
        &:hover
          cursor: pointer
          background-color: $deep_purple

    .district
      display: flex
      margin-bottom: 15px
      &:last-of-type
        margin: 0
      input
        width: fit-content
        position: absolute
        z-index: -1
        opacity: 0
        &+label
          display: inline-flex
          align-items: center
          &::before
            content: ''
            width: 0.7em
            height: 0.7em
            border: 1px solid $grey
            border-radius: 0.2em
            margin-right: 13px
        &:hover+label::before
          border-color: $purple
        &:checked+label::before
          border-color: $purple
          background: url("../../assets/icons/check.png") no-repeat center $purple
          background-size: 7px
        &:disabled+label
          color: $grey
          &::before
            border-color: $grey
            background: $grey
      label:hover
        cursor: pointer
</style>
