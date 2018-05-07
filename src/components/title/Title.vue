<template>
  <div class="title_main">
     <div class="title_select">
       <RadioGroup @on-change="onSortChange" class="sort" v-model="selectedSort">
              <Radio v-for="(item,index) in sortType" :key="index" :label="item.value"><span>{{item.label}}</span></Radio>
         </RadioGroup>
       <Input placeholder="输入题目名称" v-model="sTitle" style="width: 60%"> 
       <Button @click="onSearchTitle" slot="append" icon="ios-search"></Button>
        </Input>
        
     </div>
     <div class="title_data">
<div class="title_page">
    <div style="width:70%:height:1px"></div>
    <Page class="title_page_1" @on-change="onPageChange" :current="this.pageNum" :total="this.totalPages" simple></Page></div>
     <Collapse @on-change="onCollChange"  accordion>
        <Panel :name="item._id" v-for="(item,index) in tableData" :key="item._id">
            <span style="font-size:16px">{{((pageNum-1)*pageSize)+ index+1}}. {{item.title}}</span><Badge class-name="demo-badge-alone" style="float:right;margin:5px 30px 0 0" :count="item.clicks"> </Badge>
            <div slot="content" >
                <div style="height:200px;overflow:auto" v-html="item.answer"></div>
                <Button @click="onGoToDetail(item._id)" size="small" style="margin-left:90%;margin-top:10px">详情页</Button>
            </div>
        </Panel>
    
    </Collapse>
     </div>
  </div>
</template>

<script>
import { sortType } from "../subject/data";
import { httpTable, httpAddClicks } from "../../services/subject.service";
export default {
  name: "Title",
  data() {
    return {
      selectedType: "JS", //选中的类型
      selectedSort: "clicks", //默认的排序方式
      sTitle: "",
      totalPages: 100, // 页数
      pageNum: 1, // 当前页
      pageSize: 10, // 每页条数
      tableData: [],
      sortType: sortType,
      panelVal: 1
    };
  },
  created() {
    this.selectedType = this.$route.params.id;
    this.initTabel();
  },
  methods: {
    initTabel() {
      httpTable(this.formatPostData()).then(res => {
        this.totalPages = res.data.count;
        this.tableData = res.data.data;
      });
    },
    formatPostData() {
      return {
        page: this.pageNum,
        limit: this.pageSize,
        sort: this.selectedSort,
        type: this.selectedType
      };
    },
    onPageChange(e) {
      this.pageNum = e;
      this.initTabel();
    },
    onSearchTitle() {},
    onSortChange() {},
    onGoToDetail(id){
       this.$router.push(`/detail/${id}/?pageNum=${this.pageNum}&selectedType=${this.selectedType}`)
    },
    onCollChange(e) {
      if (e.length) {
        this.tableData.map(item => {
          if (item._id === e[0]) {
            item.clicks++;
            return item;
          }
          return item;
        });
        httpAddClicks(e[0]);
      }
    }
  },
  watch: {
    selectedSort: function(newVal) {
      this.initTabel();
    },
    "$route.params.id": function(newVal) {
      this.selectedType = newVal;
      this.initTabel();
    }
  },
  components: {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.title_main {
  width: 100%;
}
.title_select {
  width: 80%;
  display: flex;
  flex-direction: row;
  align-items: center;
  margin: 20px 0 20px 8%;
}
.title_data {
  width: 80%;
  margin: 20px 0 20px 8%;
}
.title_page {
  width: 100%;
  margin-bottom: 10px;
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
}
.title_page_1 {
  width: 30%;
}
.ivu-collapse {
  border: 1px solid #ffffff0a;
}
.ivu-collapse > .ivu-collapse-item > .ivu-collapse-header {
  padding-left: 8px !important;
}
.ivu-icon-arrow-right-b:before {
  display: none;
}
.ivu-collapse > .ivu-collapse-item {
  border-top: 1px dotted #e4e8ec;
  margin: 1px
}
.ivu-collapse-item.ivu-collapse-item-active {
  border-top: 2px solid #c8dae4;
}
.demo-badge-alone {
  background: #c5bebe !important;
}

</style>
