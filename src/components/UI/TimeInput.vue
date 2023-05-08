<template>
  <input class="inp__time" type="time" step="2" :value="modelValue" @input="updateInput">
</template>

<script>
export default {
  name: "TimeInput",
  props: {
    modelValue: [String]
  },
  methods: {
    updateInput(event){
      this.$emit("update:modelValue", event.target.value)
    }
  }
}
</script>

<style scoped>
.inp__time{

}
</style>