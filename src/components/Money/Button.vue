<template>
  <button class="button" @click="$emit('click',$event)">
    <slot />
  </button>
</template>

<script lang='ts'>
export default {
  name:"Button"
}
</script>

<style lang='scss' scoped>
@import "~@/assets/style/helper.scss";
.button {
  background: $color-blue;
  border: none;
  color: #fff;
  padding: 4px 16px;
  border-radius: 4px;

  &-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    padding-top: 16px;
    margin-top: 44-16px;
  }
}
</style>