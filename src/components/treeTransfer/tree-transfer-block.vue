<template>
  <div class="el-transfer-panel"  >
    <p class="el-transfer-panel__header">
      <el-checkbox
        v-model="allChecked"
        @change="handleAllCheckedChange"
        :indeterminate="isIndeterminate">
        {{ title }}
        <span>{{ checkedSummary }}</span>
      </el-checkbox>
    </p>

    <div class="el-transfer-panel__body" style="overflow-y:auto;" >
      <el-input v-show="showFilter"
        class="el-transfer-panel__filter"
        :disabled="disabled"
        @mouseenter.native="inputHover = true"
        @mouseleave.native="inputHover = false"
        icon="search"
        size="small"
        placeholder="输入关键字进行过滤"
        v-model="filterText">
      </el-input>
      <el-tree  class="filter-tree"
                @check = "handleTreeChecked"
                :data="data"
                node-key="id"
                show-checkbox
                :props="defaultProps"
                :filter-node-method="filterNode"
                hlight-current
                :disabled="disabled"
                ref="transferTree"
      >
      </el-tree>
    </div>
  </div>
</template>

<script>
export default {
  props: [
    //树列表框名称
    "title",
    //树列表
    "treeInfo",
    "check_info",
    //是否显示查询筛选框
    "showFilter",
    //是否显示全选按钮
    "showAllCheck",
    "disabled"

  ],
  data() {
    return {
      data:this.treeInfo ,
      checkedInfo:this.check_info,
      allChecked:false,//是否全选
      filterText:'', //搜索框文本
      defaultProps: {
        label: 'name' ,
        children: 'children'
     }
   }
 },
 computed: {
   isIndeterminate() {
     if(this.checkedInfo){
       const checkedLength = this.checkedInfo.length;
       let  dataLength = 0;
       for(let node of this.data){
         dataLength+=node.children.length;
       }
       return checkedLength > 0 && checkedLength < dataLength;
     }
     return false;

   },
   checkedSummary:function(){
     let dataLength = 0;
     for (let node of this.data) {
       dataLength += node.children.length;
     }
     if(this.checkedInfo) {
       const checkedLength = this.checkedInfo.length;
       return `${ checkedLength }/${ dataLength }`;
     }
     return `0/${ dataLength }`;;
   }
 },
 watch: {
    filterText(val){
        this.$refs.transferTree.filter(val);
    },
    check_info: {
      handler: function (val, oldVal) {
        this.checkedInfo = val;
        if (this.checkedInfo.length == 0) {
          this.allChecked = false;
        }
      },
      deep: true
    },
   treeInfo:{
     handler: function (val, oldVal) {
       this.data = [] ;
       this.data =  JSON.parse(JSON.stringify(val));
     },
     deep: true
   }
  },
 methods: {
   filterNode(value, data) {
     if (!value) return true;
     return data.name.indexOf(value) !== -1;
   },
   treeChildeChecked: function (data) {
      let index = this.checkedInfo.indexOf(data) ;
      if(index ==-1) this.checkedInfo.push(data);
      else  this.checkedInfo.splice(index,1);
   },
   treeParentChecked: function (data) {
      for(let node of data.children){
        this.treeChildeChecked(node);
      }
   },
   handleTreeChecked: function (data) {
     if (data.parent) {
       this.treeChildeChecked(data)
     } else {
       this.treeParentChecked(data)
     }
     this.$emit("listenCheckedInfoEvent",this.checkedInfo);
   },

   //全选
   handleAllCheckedChange(value) {
     if (value) {
       this.checkedInfo = this.getAllTreeChildrenNode();
       this.$refs.transferTree.setCheckedNodes(this.data);
     } else {
       this.checkedInfo = [];
       this.$refs.transferTree.setCheckedKeys([]);
     }
     this.$emit("listenCheckedInfoEvent",this.checkedInfo);
   },
   getAllTreeChildrenNode:function () {
     let children = [] ;
     for(let node of this.data){
       for(let child of node.children){
         children.push(child);
       }
     }
     return children ;
   }
 }
}
</script>
<style>
</style>
