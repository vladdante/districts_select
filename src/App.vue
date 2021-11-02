<template>
  <div id="app">
    <p>Район</p>
    <Select/>
  </div>
</template>

<script>
import Select from "./components/select"
export default {
  name: 'App',
  components: { Select }
}
</script>

<style lang="sass">
body
  display: flex
  justify-content: center
  overflow: hidden
#app
  font-family: Arial, Helvetica, sans-serif
  margin-top: 10%
</style>
