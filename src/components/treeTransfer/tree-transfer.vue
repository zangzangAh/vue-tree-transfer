<template>
    <div class="el-transfer" >
      <tree-transfer-block
        :title="titleLeft"
        :showFilter="showFilter"
        :showAllCheck="showAllCheck"
        v-on:listenCheckedInfoEvent="bindLeftCheckedInfoEvent"
        :treeInfo="transferData_left"
        v-bind:check_info="checkedData_left"
        :disabled="transferDisabled"
      ></tree-transfer-block>
      <div class="el-transfer__buttons">
        <el-button
          type="primary"
          class="el-transfer__button"
          @click.native="addToLeft"
          :disabled="checkedData_right.length === 0||transferDisabled">
          <i class="el-icon-arrow-left"></i>
        </el-button>
        <el-button
          type="primary"
          class="el-transfer__button"
          @click.native="addToRight"
          :disabled="checkedData_left.length === 0||transferDisabled">
          <i class="el-icon-arrow-right"></i>
        </el-button>
      </div>

      <tree-transfer-block
        :title="titleRight"
        :showFilter="showFilter"
        :showAllCheck="showAllCheck"
        v-on:listenCheckedInfoEvent="bindRightCheckedInfoEvent"
        :treeInfo="transferData_right"
        :disabled="transferDisabled"
        v-bind:check_info="checkedData_right">
      </tree-transfer-block>
	</div>
</template>

<script>

import treeTransferBlock from './tree-transfer-block'  //引入child组件
export default {
  components: {
    treeTransferBlock
  },
  props: [
    //左侧树列表框名称
    "titleLeft",

    //右侧树列表框名称
    "titleRight",
    //左侧树列表
    "data_left",
    //右侧树列表
    "data_right",
    //是否显示查询筛选框
    "showFilter",
    //是否显示全选按钮
    "showAllCheck",
    //禁用
    "disabled"

  ],
  data() {
      return {
        transferDisabled:this.disabled,
        transferData_left:this.data_left,
        transferData_right:this.data_right,
        //左侧树选择列表
        checkedData_left:[],
        //右侧树选择列表
        checkedData_right:[]
      }
  },

  watch: {
     disabled:{
       handler: function (val,oldVal) {
         this.transferDisabled = val;
       },
       deep: true
     },
     data_left: {
         handler: function (val,oldVal) {
           if(val!=oldVal){
             this.transferData_left = val;
           }
         },
         deep: true
       },
     data_right: {
         handler: function (val,oldVal) {
           if(val!=oldVal){
              this.transferData_right = val;
           }

         },
         deep: true
       },
   },
  methods:{
    bindLeftCheckedInfoEvent:function(data){
      this.checkedData_left = data;
    },
    bindRightCheckedInfoEvent:function(data){
      this.checkedData_right = data;
    },
    addToLeft:function(){
      this.addToLeft_rightDataAddToLeft()
      this.addToLeft_rightDataRemoveToRight()
      this.checkedData_right = [] ;
      this.$emit("listenLeftTreeInfoEvent",this.transferData_left);
      this.$emit("listenRightTreeInfoEvent",this.transferData_right);
    },
    addToRight:function(){
      this.addToRight_leftDataAddToRight();
      this.addToRight_leftDataRemoveToLeft();
      this.checkedData_left = [] ;
      this.$emit("listenLeftTreeInfoEvent",this.transferData_left);
      this.$emit("listenRightTreeInfoEvent",this.transferData_right);
    },
    addToLeft_rightDataAddToLeft:function(){
      for(let node of this.checkedData_right) {
        if (this.transferData_left.length == 0) {
          let parentNode = this.getParentNode(node);
          this.transferData_left.push(parentNode);
        } else {
          if (!this.childNodeExist(node, this.transferData_left)) {
            if (!this.parentNodeExist(node, this.transferData_left)) {
              let parentNode = this.getParentNode(node);
              this.transferData_left.push(parentNode);

            } else {
              for (let parent of this.transferData_left) {
                if (parent.id == node.parent) {
                  parent.children.push(node);
                  break;
                }
              }
            }
          }
        }
      }
    },
    addToLeft_rightDataRemoveToRight:function(){
      for(let node of this.checkedData_right){
        for(let parentNode of this.transferData_right){
          if(parentNode.id == node.parent){
            let parentIndex = this.transferData_right.indexOf(parentNode);
            let childIndex =  this.getIndex(node,parentNode.children);
            if(childIndex !=-1)  parentNode.children.splice(childIndex,1);
            if(parentNode.children.length ==0) {
              this.transferData_right.splice(parentIndex,1);
            }
          }
        }
      }
    },

    addToRight_leftDataAddToRight:function(){
      for(let node of this.checkedData_left) {
        if (this.transferData_right.length == 0) {
          let parentNode = this.getParentNode(node);
          this.transferData_right.push(parentNode);
        } else {
          if (!this.childNodeExist(node, this.transferData_right) && !this.parentNodeExist(node, this.transferData_right)) {
            let parentNode = this.getParentNode(node);
            this.transferData_right.push(parentNode);

          } else if (!this.childNodeExist(node, this.transferData_right) && this.parentNodeExist(node, this.transferData_right)) {
            for (let parent of this.transferData_right) {
              if (parent.id == node.parent) {
                parent.children.push(node);
                break;
              }
            }
          }
        }
      }
    },
    addToRight_leftDataRemoveToLeft:function(){
      for(let node of this.checkedData_left){
        for(let parentNode of this.transferData_left){
          if(parentNode.id == node.parent){
            let parentIndex = this.transferData_left.indexOf(parentNode);
            let childIndex =  this.getIndex(node,parentNode.children);
            if(childIndex !=-1) parentNode.children.splice(childIndex,1);
            if(parentNode.children.length ==0) {
              this.transferData_left.splice(parentIndex,1);
            }
          }
        }
      }
    },
    getIndex:function(node,list){
      let index = -1 ;
      for(let i in list){
        let child = list[i] ;
        if(child.id == node.id ){
          index = i ;
          break ;
        }
      }
      return index ;
    },
    parentNodeExist:function (node,list) {
      for(let item of list){
        if(node.parent == item.id) return true ;
      }
      return false ;
    },
    childNodeExist:function (node,list) {
      for(let item of list){
        if(node.parent == item.id) {
          for(let childItem of list){
            if(node.id == childItem.id) return true ;
          }
        }
      }
      return false ;
    },
    getParentNode:function(childNode){
      let parent = {'id':'','name':'','node':'','parent':null,'children':[]};
      for(let node of this.transferData_left){
        if(node.id == childNode.parent){
          parent.id = node .id;
          parent.name = node .name ;
          parent.node = node .node;
          parent.children .push(childNode);
        }
      }
      for(let node of this.transferData_right){
        if(node.id == childNode.parent){
          parent.id = node .id;
          parent.name = node .name ;
          parent.node = node .node;
          parent.children .push(childNode);
        }
      }
      return parent ;
    },

  },
  mounted(){
    if(!this.disabled){
      this.transferDisabled = false;
    }
    // this.removeTreeInfo(this.data_left,this.data_right);
  }
}
</script>
<style>
</style>
