<template>
  <layout>
    <Tabs class-prefix="type" :data-source="recordTypeList" :value.sync="type"/>
    <div class="chart-wrapper" ref="chartWrapper">
      <Charts class="chart" :options="chartOptions"></Charts>
    </div>
    <ol v-if="groupList.length>0">
      <li v-for="(group,index) in groupList" :key="index">
        <h3 class="title">{{ beautify(group.title) }}<span>￥{{ group.total }}</span></h3>
        <ol>
          <li class="record" v-for="item in group.items" :key="item.id">
            <span>{{ tagString(item.tags) }} </span>
            <span class="notes">{{ item.notes }}</span>
            <span>￥{{ item.amount }} </span>
            <!--              <span>{{ item.createdAt }}</span>-->
          </li>
        </ol>
      </li>

    </ol>
    <div v-else class="tips">
      目前没有相关记录
    </div>
  </layout>
</template>

<script lang="ts">
import Vue from 'vue';
import {Component} from 'vue-property-decorator';
import Tabs from '@/components/Tabs.vue';
import recordTypeList from '@/constants/recordTypeList';
import dayjs from 'dayjs';
import clone from '@/lib/clone';
import Charts from '@/components/Charts.vue';
import _ from 'lodash'
import day from  'dayjs'
import retryTimes = jest.retryTimes;
@Component({
  components: {Tabs,Charts},
})

export default class Statistics extends Vue {
  tagString(tags: Tag[]) {
    return tags.length === 0 ? '无' : tags.map(t=>t.name).join('，');
  }

  beautify(string: string) {
    const now = dayjs();
    const day = dayjs(string);
    if (day.isSame(now, 'day')) {
      return '今天';
    } else if (day.isSame(now.subtract(1, 'day'), 'day')) {
      return '昨天';
    } else if (day.isSame(now.subtract(2, 'day'), 'day')) {
      return '前天';
    } else if (day.isSame(now, 'year')) {
      return day.format('MM月DD日');
    } else {

      return day.format('YYYY年MM月DD日');
    }
  }

  mounted(){
    const div=(this.$refs.chartWrapper as HTMLDivElement)
    div.scrollLeft=div.scrollWidth;
  }
  get keyValueList(){
    const today =new Date();
    const array = []
    for (let i = 0;i<=29;i++){
      const date = day(today).subtract(i,'day').format("YYYY-MM-DD")
      const found = _.find(this.groupList,{title:date})
      array.push({
        date:date,value:found ? found.total : 0
      })
    }
    array.sort((a,b)=>{
      if (a.date>b.date){
        return 1
      }else if(a.date === b.date){
        return 0;
      }else {
        return -1
      }
    })
    return array
  }
  get chartOptions(){

    const keys = this.keyValueList.map(item =>item.date)
    const values = this.keyValueList.map(item=>item.value)
    return{
      tooltip:{
        show:true,
        position:'top',
        formatter: '{c}'
      },
      grid:{
        left:0,
        right:0,
      },

      xAxis: {
        axisTick:{
          show:false
        },

        type: 'category',
        data: keys,
        axisLabel:{
          formatter:function (value:string, index:number) {
            // 格式化成月/日，只在第一个刻度显示年份
            return value.substr(5);
          }
        }
      },
      yAxis: {
        type: 'value',
        show:false,
        offset:0,

      },
      series: [{
        data: values,
        type: 'line',
        showBackground: true,
        backgroundStyle: {
          color: 'rgba(220, 220, 220, 0.8)'
        },
        itemStyle:{
          borderWidth:1,
          borderColor:"#ff8a8e",
          color:"#18a0fb"

        },
        symbolSize:10,
        symbol:"circle"
      }]
    }
  }
  get recordList() {
    return (this.$store.state as RootState).recordList;
  }

  get groupList() {
    const {recordList} = this;

    const newList = clone(recordList)
        .filter(r => r.type === this.type)
        .sort((a, b) => dayjs(b.createdAt).valueOf() - dayjs(a.createdAt).valueOf());
    if (newList.length === 0) {return [];}

    type Result = { title: string; total?: number; items: RecordItem[] }[];
    const result: Result = [{title: dayjs(newList[0].createdAt).format('YYYY-MM-DD'), items: [newList[0]]}];

    for (let i = 1; i < newList.length; i++) {
      const current = newList[i];
      const last = result[result.length - 1];

      if (dayjs(last.title).isSame(dayjs(current.createdAt), 'day')) {
        last.items.push(current);
      } else {
        result.push({title: dayjs(current.createdAt).format('YYYY-MM-DD'), items: [current]});
      }
    }
    result.map(group => {
      group.total = group.items.reduce((sum, item) => sum + item.amount, 0);
    });
    return result;
  }

  beforeCreate() {
    this.$store.commit('fetchRecords');
  }

  type = '-';
  recordTypeList = recordTypeList;
}
</script>

<style lang="scss" scoped>
@import "~@/assets/style/helper.scss";
.echarts{
  max-width:100%;
  margin:0 auto;
}
::v-deep {
  .type-tabs-item {
    background: #e8e8e9;
    color: $color-highlight;

    &.selected {
      background: $color-highlight;
      color: #fff;

      &::after {
        display: none;
      }
    }
  }

  .interval-tabs-item {
    height: 48px;
    font-size: 14px;
  }
}

%item {
  padding: 0 16px;
  min-height: 40px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.title {
  @extend %item
}

.record {
  background: #fff;
  @extend %item;
}

.notes {
  margin-right: auto;
  margin-left: 10px;
  color: #999;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.tips{
  text-align: center;
  padding:16px;
}

.chart{
  width: 430%;
  &-wrapper{
    overflow: auto;
    &::-webkit-scrollbar{
      display: none;
    }
  }
}

</style>