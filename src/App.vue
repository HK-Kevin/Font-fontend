<template>
  <div id="app">
     <header> 
       <div class="app_header">
          <h1>Font 题库</h1>
       </div>
     </header>
  <div class="content">
      <Menu :theme="'light'" @on-select="onSlectMenu"  :active-name="activeMenu">
            <MenuItem :name="item.name" v-for="item in menu" :key="item.name">
                <Icon :type="item.icon"></Icon>
                   {{ item.name}}
            </MenuItem>
       </Menu>
      <div class="router">
        <router-view/>
    
      </div>
  </div>
   
  </div>
</template>

<script>
import {MENU,types} from './assets/config'
export default {
  name: "App",
    data () {
    return {
      menu:MENU,
      activeMenu:'HTML5'
    }
  },
  created(){
    const id = this.$route.params.id 
    const bId =  this.$route.query.selectedType;
    
   if(types.includes(id)){
      this.activeMenu =  id
   }else if(types.includes(bId)){
      this.activeMenu =  bId
   }
  },
  methods:{
    onSlectMenu(e){
     this.$router.push(`/title/${e}`)      
    }
  }
};
</script>

<style>
#app{
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}
.app_header {
  width: 100%;
  height: 50px;
  background-color: #1995dc;
}
.app_header h1 {
  color: white;
  line-height: 50px;
  padding-left: 10%;
  padding-right:10% 
}
.content {
  flex: 1;
  display: flex;
  padding-left: 10%;
  padding-right:10% ;
  flex-direction: row;
  border: 1 solid gray
}
Menu{
  width: 30%;
}
.content div.router {
  flex: 1;
}
</style>
