<template>
  <input class="inp__time" type="date" :value="modelValue" @input="updateInput">
</template>

<script>
export default {
  name: "DateInput",
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