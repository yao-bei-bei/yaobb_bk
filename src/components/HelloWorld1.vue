<template>
  <div class="box">
    <div  v-popover:popover2>选择区域1</div>
    <el-popover
      ref="popover2"
      placement="bottom"
      width="1000"
      trigger="click">
      <el-cascader
        style="width: 100%"
        class="box"
        @change="deleteNode"
        v-model="value10"
        :options="options"
        :show-all-levels="false"
        :clearable="true"
        :props="{ multiple: true,checkStrictly:true}"
        clearable></el-cascader>
      <el-tree
        style="overflow: auto;height: 190px;z-index: 999"
        ref="tree"
        :data="options"
        :props="{ children: 'children', label: 'label', }"
        node-key="value"
        show-checkbox
        @check="handleCheckChange('tree')"
      ></el-tree>
    </el-popover>
    <div  v-popover:popover3>选择区域2</div>
    <el-popover
      style="width: 100%"
      ref="popover3"
      placement="bottom"
      width="1000"
      trigger="click">
      <el-cascader
        class="box"
        style="width: 100%"
        @change="deleteNode1"
        v-model="value11"
        :options="options"
        :show-all-levels="false"
        :clearable="true"
        :props="{ multiple: true,checkStrictly:true}"
        clearable></el-cascader>
      <el-tree
        style="overflow: auto;height: 190px;z-index: 999"
        ref="trees"
        :data="options"
        :props="{ children: 'children', label: 'label', }"
        node-key="value"
        show-checkbox
        @check="handleCheckChange('trees')"
      ></el-tree>
    </el-popover>
  </div>
</template>
<script>
  import options from '../api/country-data'
  complateOptions(options);
  function complateOptions(options, prefix) {
    options.forEach(item => {
      item.value = !prefix ? item.value :prefix + "," + item.value
      if (item.children) complateOptions(item.children, item.value);
    });
    return options;
  }
  function getOptimizeCheckNodeValueList(root) {
    const result = [];
    function main(node) {
      if (node.childNodes.length === 0) return;
      const childNodes = node.childNodes;
      let i = 0;
      for (; i < childNodes.length; i++) {
        if (childNodes[i].checked) {
          result.push(childNodes[i].data.value); // 优化：直接返回结果，不再返回节点，因为用不到。。
        } else {
          main(childNodes[i]);
        }
      }
    }
    main(root);
    return result;
  }
  export default {
    data() {
      this.options = options;
      return {
        selectNodeValueList: [],
        value10:[],
        value11:[]
      };
    },
    methods: {
      handleCheckChange(tree) {
        let arr=[];
        this.selectNodeValueList = getOptimizeCheckNodeValueList(this.$refs[tree].store.root);
        if(tree=='tree'){
          f(this.selectNodeValueList);
          this.value10=arr;
        }else{
          f(this.selectNodeValueList);
          this.value11=arr
        }
        function f(data) {
          data.map(i=>{
            let arrs=i.split(',');
            let v1=arrs[0],
              v2=arrs[0]+','+arrs[1],
              v3=arrs[0]+','+arrs[1]+','+arrs[2],
              v4=arrs[0]+','+arrs[1]+','+arrs[2]+','+arrs[3];
            if(arrs.length==1){
              arr.push(v1)
            }
            if(arrs.length==2){
              arr.push([v1,v2])
            }
            if(arrs.length==3){
              arr.push([v1,v2,v3])
            }
            if(arrs.length==4){
              arr.push([v1,v2,v3,v4])
            }
          });
        }

      },
      deleteNode1(index){
        let arr=[];
        index.map(i=>{
          arr.push(i[i.length-1])
        })
        this.$refs.trees.setCheckedKeys(arr);
      },
      deleteNode(index) {
        let arr=[];
        index.map(i=>{
          arr.push(i[i.length-1])
        })
        this.$refs.tree.setCheckedKeys(arr);
      }
    }
  };
</script>
<style>
  .el-cascader__dropdown{
    z-index: 100!important;
  }
</style>
