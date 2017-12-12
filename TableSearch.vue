<!-- 表格搜索组件 -->
<template>
  <Input
    placeholder="请选择列名并输入内容..."
    v-model="searchContent"
    @on-change="handleSearch">
    <Select slot="prepend" style="width: 80px" v-model="searchColumn">
        <Option
          v-for="column in searchColumns"
          :value="column.key"
          :key="column.key">
          {{column.title}}
        </Option>
    </Select>
  </Input>
</template>

<script>
  export default{
    name: 'TableSearch',
    props: [
      'tableData',  // 表格数据
      // 需要搜索的表格列的列描述数据对象数组
      // 对象需要包含key, title两个属性
      // 可参照iview table组件的column参数, https://www.iviewui.com/components/table#column
      'searchColumns'
    ],
    data() {
      return {
        searchContent: '',  // 当前搜索内容
        tableDataClone: [],  // 表格数据克隆
        // 当前搜索的表格列的key，默认为searchColumns的第一个元素的key
        'searchColumn': this.searchColumns[0]['key']
      }
    },
    methods: {
      // 表格搜索函数，可支持多列搜索
      search: function (data, argumentObj) {
        let res = data;
        let dataClone = data;
        for (let argu in argumentObj) {
          if (argumentObj[argu].length > 0) {
            res = dataClone.filter(d => {
              return d[argu].indexOf(argumentObj[argu]) > -1;
            });
            dataClone = res;
          }
        }
        return res;
      },
      handleSearch: function () {
        var argumentObjStr = '{"' + this.searchColumn + '": "' + this.searchContent + '"}' // 拼接json
        var argumentObj = JSON.parse(argumentObjStr) // 转为对象
        var res = this.search(this.tableDataClone, argumentObj) // 执行搜索，获取搜索结果
        this.$emit('update:tableData', res) // 更新表格数据为搜索结果
      }
    },
    watch: {
      // 因父组件表格数据通常存在异步操作
      // 需监听props以得到正确的数据
      tableData: function (newTableData) {
        if (newTableData.length > 0 && this.tableDataClone.length == 0) {
          this.tableDataClone = newTableData
        }
      }
    }
  }
</script>
