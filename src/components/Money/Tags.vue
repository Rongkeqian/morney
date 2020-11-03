<template>
  <div class="tags">
    <div class="new">
      <button @click="createTag">新增标签</button> <!--     首页新建-->
    </div>
    <ul class="current">
      <li v-for="tag in tagList" :key="tag.id"
          :class="{selected:selectedTags.indexOf(tag)>=0}"
          @click="toggle(tag)">{{ tag.name }}
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component} from 'vue-property-decorator';
import TagHelper from '@/mixins/TagHelper';
import { mixins } from 'vue-class-component';

@Component
export default class Tags extends mixins(TagHelper) {
  selectedTags: string[] = [];

  get tagList(){
    return this.$store.state.tagList;
  }
  created(){
    this.$store.commit('fetchTags')
  }

  toggle(tag: string) {
    const index = this.selectedTags.indexOf(tag);
    if (index >= 0) {
      this.selectedTags.splice(index, 1);
    } else {
      this.selectedTags.push(tag);
    }
    this.$emit('update:value', this.selectedTags);
  }

}
</script>

<style lang="scss" scoped>
@import "~@/assets/style/helper.scss";

.tags {
  font-size: 14px;
  padding: 16px;
  flex-grow: 1;
  display: flex;
  flex-direction: column-reverse;
  background: #fff;

  > .current {
    display: flex;
    flex-wrap: wrap;

    > li {
      background: $color-d9;
      $h: 24px;
      height: $h;
      border-radius: $h/2;
      padding: 0 16px;
      margin-right: 12px;
      margin-bottom: 4px;
      display: flex;
      justify-content: center;
      align-items: center;

      &.selected {
        background: $color-highlight;
        color: $color-white;
      }
    }
  }

  > .new {
    padding-top: 16px;

    button {
      background: transparent;
      border: none;
      border-bottom: 2px solid;
      color: $color-9;
      padding: 0 3px;
    }
  }
}
</style>