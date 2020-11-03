<template>
  <Layout class-prefix="layout">
    <Tags @update:value="record.tags =$event"/>
    <div class="createdAt">
      <FormItem field-name="日期" type="date" placeholder="请输入备注" :value.sync="record.createdAt"/>
    </div>

    <div class="notes">
      <FormItem field-name="备注" placeholder="请输入备注" :value.sync="record.notes"/>
    </div>
    <Tabs :data-source="recordTypeList"
          :value.sync="record.type"/>
    <NumberPad @update:value="onUpdateAmount" @submit="saveRecord"/>
  </Layout>
</template>

<script lang="ts">
import Vue from 'vue';
import Tags from '@/components/Money/Tags.vue';
import NumberPad from '@/components/Money/NumberPad.vue';
import FormItem from '@/components/Money/FormItem.vue';
import {Component} from 'vue-property-decorator';
import recordTypeList from '@/constants/recordTypeList';
import Tabs from '@/components/Tabs.vue';

@Component({
  components: {Tabs, Tags, FormItem,  NumberPad},

})
export default class Money extends Vue {
  record: RecordItem = {tags: [], notes: '', type: '-', amount: 0,createdAt:new Date().toISOString()};

  get recordList(){
    return this.$store.state.count;
  }
  recordTypeList=recordTypeList
  created() {
    this.$store.commit('fetchRecords')
  }

  onUpdateNotes(value: string) {
    this.record.notes = value;
  }

  onUpdateAmount(value: string) {
    this.record.amount = parseFloat(value);
  }

  saveRecord() {
    if(this.record.tags.length===0||!this.record.tags){
      return window.alert('请至少选择一个标签')
    }
    this.$store.commit('createRecord', this.record);
    if(this.$store.state.createRecordError === null){
      window.alert("保存成功")
      this.record.notes = ''
    }
  }
}
</script>
<style lang="scss">
.layout-content {
  display: flex;
  flex-direction: column;
}
.notes {
  padding: 10px 0;
}

</style>
