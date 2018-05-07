<template>
  <div class="title_detail">
      <list></list>
      <div class="title_title">
      <h2 >{{title.title}}</h2>
   </div>
   <div class="title_info">
      <div class="title_important">
          <Rate disabled v-model="title.important"></Rate>
      </div>
      <div class="title_clicks">
          <Badge :count="title.clicks"></Badge>
      </div>
        <div class="title_tags">
       <Tag v-for="item in title.company" :key="item" :name="item" >{{item}}</Tag>

        </div>
   </div>
   <div class="title_answer">
       <h4 style="30px">参考答案：</h4>  
        <div style="height:calc(100% - 30px);width:100%;overflow:auto" class="quill-code">
          <code class="hljs" v-html="title.answer"></code>
        </div> 
   </div>
   <div class="title_reference">
      <h4>参考资源：</h4>  
      <div>
            <Alert 
            type="success" 
            v-for="item in title.reference"
            :key="item"
            >{{item}}</Alert>
      </div>
   </div>
  </div>
</template>

<script>
import hljs from "highlight.js";
//import 'highlight.js/styles/github.css' //样式文件
import "highlight.js/styles/atom-one-light.css"; //样式文件
import List from "./List";
import { httpOne } from "../../services/subject.service";
export default {
  name: "Detail",
  data() {
    return {
      id: "",
      title: {},
      content: `<h2 class="ql-align-center"><span class="ql-font-serif">Text content loading..</span></h2>`
    };
  },
  created() {
    this.id = this.$route.params.id;
    this.initOneTitle();
  },
  computed: {
    contentCode() {
      return hljs.highlightAuto(this.title.answer).value;
    }
  },
  methods: {
    initOneTitle() {
      httpOne(this.id).then(res => {
        this.title = res.data;
      });
    }
  },
  watch: {
    "$route.params.id": function(newVal) {
       httpOne(newVal).then(res => {
        this.title = res.data;
      });
    }
  },
  components: { List }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.title_detail {
  width: 100%;
  height: 100%;
  display: flex;

  flex-direction: column;
}
.title_title {
  height: 30px;
  width: 80%;
  margin: 20px 0 20px 8%;
}
.title_title h2 {
  height: 30px;
  padding-left: 10px;
  border-left: 2px solid blue;
}
.title_info {
  height: 50px;
  width: 80%;
  border-bottom: 2px solid grey;
  margin: 0 0 20px 8%;
  display: flex;
  flex-direction: row;
  align-content: center;
}
.title_info Badge {
  display: block;
  width: 100px;
}
.title_important {
  width: 33%;
}
.title_clicks {
  padding-top: 5px;
  width: 33%;
}
.title_tags {
  flex: 1;
}
.title_answer {
  height: 60%;
  margin-left: 8%;
  width: 80%;
   overflow-y: auto; 
}
.title_reference {
  margin-left: 8%;
  width: 80%;
  border-top: 2px solid grey;
}
.title_answer_wapper {
  background-color: inherit;
  margin-left: 70px;
}
pre {
  background-color: #f5edde;
}
.ivu-alert-success {
  border: 1px solid #dfe6e3;
  background-color: #ffffff;
}
</style>
