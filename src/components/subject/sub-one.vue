<template>
  <div class="update">
     <Form :model="subject" :label-width="80" >
        <FormItem label="题目">
            <Input v-model="subject.title" placeholder="Enter something..."/>
        </FormItem>
        <FormItem label="类型">
            <Select style="width:500px" v-model="subject.type">
                <Option v-for="(item,index) in types" :value="item" :key="index">{{item}}</Option>
            </Select>
        </FormItem>
        <FormItem label="重要性">
            <Rate v-model="subject.important"></Rate>
        </FormItem>
        <FormItem label="公司">
             <Input v-model="subject.company" placeholder="Enter something..." />
        </FormItem>
    
        <FormItem label="难度">
           <InputNumber :max="10" :min="1" v-model="subject.hard"></InputNumber>
        </FormItem>
         <FormItem label="参考">
           <Button  @click="addRef" icon="plus"></Button>
             <div>
               <Input
                 style="margin:3px 0"
                 v-for="(item,index) in subject.refernce" 
                 :key="index" 
                 v-model="subject.refernce[index]" 
                 placeholder="Enter something..." >
                   <Button slot="append" @click="deleteRef(index)" icon="ios-minus-empty"></Button>
               </Input>
             </div>
        </FormItem>
        <FormItem label="编辑答案">
           <quill-editor style="height:300px;width:100%" ref="myTextEditor"
              v-model="subject.answer">
              </quill-editor>
        </FormItem>
         </Form>   
          <div class="btn">
             <Button type="primary" @click="onSubmit">提交</Button>
             <Button type="ghost" style="margin-left: 8px" @click="onGoSubejct">返回</Button>
             <Button type="info" style="margin-left: 8px" @click="modal=true">预览答案</Button>
          </div>
          <Modal
        title="预览答案"
        v-model="modal"
        :styles="{top: '20px',width:'800px',height:'500px'}">
        <div class="answer_html" v-html="subject.answer"></div>
    </Modal>
  </div>
</template>

<script>
import "quill/dist/quill.core.css";
import "quill/dist/quill.snow.css";
import "quill/dist/quill.bubble.css";
import { quillEditor } from "vue-quill-editor";
import { type } from "./data";
import { httpAdd, httpOne,httpUpdate } from "../../services/subject.service";
export default {
  name: "sub-one",
  props: [],
  data() {
    return {
      id: "",
      types: type,
      modal: false,
      subject: {
        title: "",
        type: "",
        company: "",
        refernce:[''],
        important: 3,
        hard: 5,
        answer: "<h2>I am Example</h2>"
      }
    };
  },
  created() {
    this.id = this.$route.params.id;
    if (this.id === "add") {
      return;
    } else {
      httpOne(this.id).then(res => {
        this.subject = res.data;
      });
    }
  },
  methods: {
    onSubmit() {
      if (this.id === "add") {
        httpAdd(this.subject).then(res => {
          if (res.data.result) {
            this.$Message.success("添加成功");
          } else {
            this.$Message.error("添加失败");
          }
        });
        return;
      } else {
        httpUpdate(this.subject).then(res => {
        if (res.data.result) {
            this.$Message.success("更新成功");
          } else {
            this.$Message.error("更新失败");
          }
        });
      }
    },
    onGoSubejct() {
      this.$router.go(-1);
    },
    addRef(){
      this.subject.refernce.push('')
    },
    deleteRef(index){
      console.log(123)
      this.subject.refernce.splice(index,1)
    }
  },
  components: { quillEditor }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.update {
  margin-top: 10px;
}
.btn {
  margin: 140px 0 0 80px;
}
.answer_html {
  height: 600px;
  overflow: auto;
}
</style>
