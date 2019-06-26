<template>
  <div style="margin:0 auto;width:50%;">
    <h3 style="color:#20A0FF">VUE——树形结构穿梭框</h3>
    <hr>
    <div style="margin:0 auto;padding-top:40px;width:600px;height:380px;text-align: left">

        <tree-transfer
          :titleLeft="titleLeft"
          :titleRight="titleRight"
          :data_left="transferData_left_area"
          :data_right="transferData_right_area"
          :showFilter="showFilter"
          :showAllCheck="showAllCheck"
          :disabled="disabled"
          @listenRightTreeInfoEvent="bindRightTreeInfoEvent"
          @listenLeftTreeInfoEvent="bindLeftTreeInfoEvent"
        ></tree-transfer>

    </div>
    <hr>
    <div style="text-align: left;font-size: 10px" >
      <ul>
        <li  style="font-weight: bold;color:red">基于VUE-ELEMENT组件</li>
        <li>示例以地域数据为例，可根据实际项目需要替换数据（仅支持两层树结构）</li>
        <li> 数据格式：<span style="color:blue;font-weight: bolder">[{"id":id,"name":name,"code":code,"parent":parent,"children":[]}]</span></li>
      </ul>

    </div>

  </div>
</template>

<script>
  export const areaTree = [{"id":63,"name":"北京","code":"cn010","parent":null,"children":[{"id":211,"name":"北京市","code":"010","parent":63,"children":null}]},{"id":64,"name":"上海","code":"cn021","parent":null,"children":[{"id":213,"name":"上海市","code":"021","parent":64,"children":null}]},{"id":67,"name":"河北","code":"cn0311","parent":null,"children":[{"id":221,"name":"邯郸市","code":"0310","parent":67,"children":null},{"id":222,"name":"石家庄市","code":"0311","parent":67,"children":null},{"id":223,"name":"保定市","code":"0312","parent":67,"children":null},{"id":224,"name":"张家口市","code":"0313","parent":67,"children":null}]},{"id":68,"name":"山西","code":"cn0351","parent":null,"children":[{"id":226,"name":"太原市","code":"0351","parent":68,"children":null},{"id":384,"name":"朔州市","code":"0349","parent":68,"children":null},{"id":385,"name":"忻州市","code":"0350","parent":68,"children":null}]},{"id":69,"name":"内蒙古","code":"cn0471","parent":null,"children":[{"id":237,"name":"呼和浩特市","code":"0471","parent":69,"children":null},{"id":238,"name":"赤峰市","code":"0476","parent":69,"children":null},{"id":428,"name":"包头市","code":"0472","parent":69,"children":null}]},{"id":70,"name":"辽宁","code":"cn024","parent":null,"children":[{"id":216,"name":"沈阳市","code":"024","parent":70,"children":null},{"id":230,"name":"大连市","code":"0411","parent":70,"children":null}]}]
  import treeTransfer  from './treeTransfer/tree-transfer'

  export default {
    components:{  treeTransfer  },
    name: 'HelloWorld',
    data () {
      return {
        disabled:false,
        titleLeft: '地域列表', //左侧显示标题
        titleRight: '已选择列表',//右侧选择标题
        transferData_left_area:[],//左侧选择数据
        transferData_right_area:[],//右侧选择数据
        showFilter:true,//是否显示模糊查询
        showAllCheck:true, //是否可全选
      }
    },
    methods:{
      bindRightTreeInfoEvent:function(data){
        this.transferData_right_area = data;
        console.log("右侧的数据:"+JSON.stringify(data));
      },
      bindLeftTreeInfoEvent:function(data){
        this.transferData_left_area = data;
        console.log("左侧的数据:"+JSON.stringify(data));
      },
    },
    mounted() { this.transferData_left_area = JSON.parse(JSON.stringify(areaTree)) },
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
 
</style>
