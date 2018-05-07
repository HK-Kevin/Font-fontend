<template>
  <div class="list_main">
     <Scroll  height="400px">
        <div dis-hover v-for="(item, index) in tableData" :key="index" style="margin: 10px 0">
          <p @click="onGoToDetail(item._id)" class="list_item" :title="item.title" v-if="$route.params.id ===item._id" style="color:#1995dc;font-size:14px"> {{((pageNum-1)*pageSize)+ index+1}}.  {{ item.title }}</p> 
          <p @click="onGoToDetail(item._id)" class="list_item" :title="item.title" v-else> {{((pageNum-1)*pageSize)+ index+1}}.  {{ item.title }}</p> 
        </div>
    </Scroll>
   <ButtonGroup>
        <Button @click="onChangePage(-1)" type="primary">
            <Icon type="chevron-left"></Icon>
            Back
        </Button>
        <Button @click="onChangePage(1)" type="primary">
            Next    
            <Icon type="chevron-right"></Icon>
        </Button>
    </ButtonGroup>
  </div>
</template>

<script>
import { sortType } from "../subject/data";
import { httpTable, httpAddClicks } from "../../services/subject.service";
export default {
  name: "List",
  data() {
    return {
      selectedType: "", //选中的类型
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
    this.selectedType = this.$route.query.selectedType;
    this.pageNum = this.$route.query.pageNum;
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
    goTo(id){
       this.$router.push(`/detail/${id}?pageNum=${this.pageNum}&selectedType=${this.selectedType}`) 
    },
    onSearchTitle() {},
    onSortChange() {},
    onGoToDetail(id) {
      this.$router.push(`/detail/${id}?pageNum=${this.pageNum}&selectedType=${this.selectedType}`);
    },
  onChangePage(n){
      
      if(n>0){
          if(this.pageNum >= Math.ceil(this.totalPages/10))return
          this.pageNum++
      }else{
          if(this.pageNum <= 1){
              this.pageNum = 2
          }
          this.pageNum = this.pageNum - 1
      }
      this.initTabel()
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
    }
    
  },
  components: {}
};
</script>

<style>
.list_main {
  position: absolute;
  right: 0;
  top: 200px;
  width: 150px;
}
.list_item:hover{
    padding-left: 10px ;
    font-size: 14px;
    color: #1995dc
}
.list_item{
white-space: nowrap;
 cursor: pointer;
}
</style>
