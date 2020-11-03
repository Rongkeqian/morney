<template>
  <div>
    <layout>
      <div class="tagsList">
        <router-link class="tagItem" :to="`labels/edit/${tag.id}`" v-for="tag in tags" :key="tag.id">
          <span>{{ tag.name }}</span>
          <Icon name="right"/>
        </router-link>

      </div>
      <div class="createTags-wrapper">
        <Button class="createTags" @click="createTag">新建标签</Button>
      </div>
    </layout>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component} from 'vue-property-decorator';
import Button from '@/components/Money/Button.vue';
import { TagHelper } from '@/mixins/TagHelper';
import { mixins } from 'vue-class-component';


@Component({
  components: {Button},
  mixins:[TagHelper],
})
export default class Labels extends mixins(TagHelper) {
  get tags(){
    return this.$store.state.tagList;
  }
  beforeCreate() {
    this.$store.commit('fetchTags');  //标签页新建
  }

}
</script>


<style lang="scss" scoped>
@import "~@/assets/style/helper.scss";

.tagsList {
  background: #ffffff;
  font-size: 16px;
  padding-left: 16px;

  > .tagItem {
    min-height: 44px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 1px solid #e6e6e6;

    svg {
      color: #666;
      margin-right: 16px;
      font-size: 18px;
    }
  }
}

.createTags {
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