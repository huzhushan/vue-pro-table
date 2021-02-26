# vue-pro-table
一个基于ElementUI封装的table列表页组件，将包含搜索、列表、分页等功能的页面封装成一个组件

## 特性

- 将搜索、列表、分页三者的交互逻辑封装到组件中，节省开发者代码量
- 配置化的表格项，跟el-table-column的配置属性类似
- 配置化的搜索表单，支持大部分表单元素
- 自定义是否显示搜索和分页
- 丰富的插槽提供功能扩展

## 使用

### 安装vue-pro-table

```js
// npm
npm install vue-pro-table
// yarn 
yarn add vue-pro-table
```

### 快速使用

```vue
<template>
  <pro-table
    title="列表"
    :request="getList"         
    :columns="columns"
    :search="searchConfig"
    :pagination="paginationConfig"           
  >
    <template #operate="scope">
      <el-button size="mini" type="primary">编辑</el-button>
      <el-button size="mini" type="danger">删除</el-button>
    </template>
  </pro-table>
</template>

<script>
import ProTable from 'vue-pro-table'    
export default {
  components: {
    ProTable
  },
  data() {
    // 表格列配置，大部分属性跟el-table-column配置一样
    columns: [
      { label: '序号', type: 'index' },
      { label: '名称', prop: 'nickName', width: 180 },
      { label: '邮箱', prop: 'userEmail' },
      {
        label: '操作',
        fixed: 'right',
        width: 180,
        align: 'center',
        tdSlot: 'operate', // 自定义单元格内容的插槽名称
      },
    ],
    // 搜索配置
    searchConfig: {
      labelWidth: '90px',  // label文字长度
      inputWidth: '360px', // 表单项长度
      fields: [ // 搜索字段
        {
          type: 'text',
          label: '名称',
          name: 'nickName'
        },
      ]
    },
    // 分页配置  
    paginationConfig: {
      layout: 'total, prev, pager, next, sizes', // 分页组件显示哪些功能
      pageSize: 5, // 每页条数
      pageSizes: [5, 10, 20, 50], 
    }
  },
  methods: {
    async getList(params) {
      // params是从组件接收的，包含分页和搜索字段。
      const { data } = await getUserList(params)
      
      // 必须要返回一个对象，包含data数组和total总数		
      return {
        data: data.list,
        total: +data.total,
      }
    },
  }
}
</script>
```

预览效果

> 默认不包含搜索表单

![列表页](https://raw.githubusercontent.com/huzhushan/pics/master/vue-pro-table/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20210226101705.png)

### 配置项



