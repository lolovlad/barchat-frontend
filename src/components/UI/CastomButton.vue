<template>
  <button class="btn__main">
    <slot></slot>
  </button>
</template>

<script>
export default {
  name: "CastomButton"
}
</script>

<style scoped>
  .btn__main{
    border-radius: 0;
    color: white;
    background: black;
    border: 0;
    padding: 10px;
  }
</style>