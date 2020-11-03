<template>
  <div>

    <label class="FormItem">
      <span class="name">{{ this.fieldName }}</span>
      <template v-if="type === 'date'">
        <input :type="type || 'text'"
               :value="x(value)"
               @input="onValueChanged($event.target.value)"
               :placeholder="this.placeholder">
      </template>
      <template v-else>
        <input :type="type || 'text'"
               :value="value"
               @input="onValueChanged($event.target.value)"
               :placeholder="this.placeholder">
      </template>
    </label>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component, Prop} from 'vue-property-decorator';
import dayjs from 'dayjs';

@Component
export default class FormItem extends Vue {
  @Prop({default:''}) readonly value!: string;
  @Prop({required: true}) fieldName!: string;
  @Prop() placeholder?: string;
  @Prop()type?: string;

  onValueChanged(value: string) {
    this.$emit('update:value', value);
  }
  x(isoString: string){
    return dayjs(isoString).format('YYYY-MM-DD')
  }

}
</script>

<style lang="scss" scoped>
@import "~@/assets/style/helper.scss";

.FormItem {
  font-size: 14px;
  padding: 2px 16px;
  display: flex;
  align-items: center;

  .name {
    margin-right: 16px;
  }

  input {
    height: 44px;
    flex: 1;
    border: none;
    background: transparent;
  }
}
</style>