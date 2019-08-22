<template>

  <div id="app">

<div style="max-width: 500px; margin: auto">
  <b-form-group>
    <template slot="label">

<h1 style="text-align: center">Quality of Life</h1>

      <br>
      <b-form-checkbox
        v-model="allSelected"

        aria-describedby="flavours"
        aria-controls="flavours"
        @change="toggleAll"
      >
        {{ allSelected ? 'Un-select All' : 'Select All' }}
      </b-form-checkbox>
    </template>

    <b-form-checkbox-group
      id="flavors"
      v-model="selected"
      :options="flavours"
      name="flavors"
      class="ml-4"
      aria-label="Individual flavours"
      stacked
    ></b-form-checkbox-group>
  </b-form-group>

  <div>
    Selected: <strong>{{ selected }}</strong><br>

  </div>
</div>

    </div>




</template>

<script>


export default {
  el:"#app",
    data() {
      return {
        flavours: [
          'Orange',
          'Grape',
          'Apple',
          'Lime'],
        selected: [],
        allSelected: false,

      }
    },
    methods: {
      toggleAll(checked) {
        this.selected = checked ? this.flavours.slice() : []
      }
    },
    watch: {
      selected(newVal, oldVal) {
        // Handle changes in individual flavour checkboxes
        if (newVal.length === 0) {
          this.indeterminate = false
          this.allSelected = false
        } else if (newVal.length === this.flavours.length) {
          this.indeterminate = false
          this.allSelected = true
        } else {
          this.indeterminate = true
          this.allSelected = false
        }
      }
    }
  }
</script>

<style >

body{

}

</style>
