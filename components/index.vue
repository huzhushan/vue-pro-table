<!--
 * @Descripttion: 基于ElementUI封装的table列表页组件（包含搜索、列表、分页功能）
 * @version: V1.0.0
 * @Author: huzhushan@126.com
 * @Date: 2021-02-04 13:24:16
 * @LastEditors: zsen.hu@uni-chain.com
 * @LastEditTime: 2021-03-02 14:07:18
-->
<template>
  <div class="page-box">
    <!-- 搜索选项 -->

    <el-form
      v-if="!!search"
      class="search"
      :model="searchModel"
      :inline="true"
      label-position="left"
      :label-width="search.labelWidth"
      ref="searchForm"
    >
      <el-form-item
        v-for="item in search.fields"
        :key="item.name"
        :label="item.label"
        :prop="item.name"
      >
        <el-select
          v-if="item.type === 'select'"
          v-model="searchModel[item.name]"
          :filterable="!!item.filterable"
          :multiple="!!item.multiple"
          clearable
          :placeholder="`请选择${item.label}`"
          :style="{ width: search.inputWidth, ...item.style }"
        >
          <el-option
            v-for="option of item.options"
            :key="option.value"
            :label="option.name"
            :value="option.value"
          ></el-option>
        </el-select>
        <el-radio-group
          v-model="searchModel[item.name]"
          v-else-if="item.type === 'radio'"
          :style="{ width: search.inputWidth, ...item.style }"
        >
          <el-radio
            v-for="option of item.options"
            :key="option.value"
            :label="option.value"
            >{{ option.name }}</el-radio
          >
        </el-radio-group>
        <el-radio-group
          v-model="searchModel[item.name]"
          v-else-if="item.type === 'radio-button'"
          :style="{ width: search.inputWidth, ...item.style }"
        >
          <el-radio-button
            v-for="option of item.options"
            :key="option.value"
            :label="option.value"
            >{{ option.name }}</el-radio-button
          >
        </el-radio-group>
        <el-checkbox-group
          v-model="searchModel[item.name]"
          v-else-if="item.type === 'checkbox'"
          :style="{ width: search.inputWidth, ...item.style }"
        >
          <el-checkbox
            v-for="option of item.options"
            :key="option.value"
            :label="option.value"
            >{{ option.name }}</el-checkbox
          >
        </el-checkbox-group>
        <el-checkbox-group
          v-model="searchModel[item.name]"
          v-else-if="item.type === 'checkbox-button'"
          :style="{ width: search.inputWidth, ...item.style }"
        >
          <el-checkbox-button
            v-for="option of item.options"
            :key="option.value"
            :label="option.value"
            >{{ option.name }}</el-checkbox-button
          >
        </el-checkbox-group>
        <el-date-picker
          v-else-if="item.type === 'date'"
          v-model="searchModel[item.name]"
          type="date"
          value-format="yyyy-MM-dd"
          :placeholder="`请选择${item.label}`"
          :style="{ width: search.inputWidth, ...item.style }"
        ></el-date-picker>
        <el-date-picker
          v-else-if="item.type === 'datetime'"
          v-model="searchModel[item.name]"
          type="datetime"
          value-format="yyyy-MM-dd HH:mm:ss"
          clearable
          :placeholder="`请选择${item.label}`"
          :style="{ width: search.inputWidth, ...item.style }"
        ></el-date-picker>
        <el-date-picker
          v-else-if="item.type === 'daterange'"
          v-model="searchModel[item.name]"
          type="daterange"
          value-format="yyyy-MM-dd"
          range-separator="-"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          clearable
          @change="handleDateChange($event, item)"
          :style="{ width: search.inputWidth, ...item.style }"
        ></el-date-picker>
        <el-date-picker
          v-else-if="item.type === 'datetimerange'"
          v-model="searchModel[item.name]"
          type="datetimerange"
          value-format="yyyy-MM-dd HH:mm:ss"
          range-separator="-"
          start-placeholder="开始时间"
          end-placeholder="结束时间"
          clearable
          @change="handleDateChange($event, item)"
          :style="{ width: search.inputWidth, ...item.style }"
        ></el-date-picker>
        <el-input-number
          v-else-if="item.type === 'number'"
          v-model="searchModel[item.name]"
          :placeholder="`请输入${item.label}`"
          controls-position="right"
          :min="item.min"
          :max="item.max"
          :style="{ width: search.inputWidth, ...item.style }"
        />
        <el-input
          v-else-if="item.type === 'textarea'"
          type="textarea"
          v-model="searchModel[item.name]"
          clearable
          :placeholder="`请输入${item.label}`"
          :style="{ width: search.inputWidth, ...item.style }"
        ></el-input>
        <el-input
          v-else
          v-model="searchModel[item.name]"
          :placeholder="`请输入${item.label}`"
          :style="{ width: search.inputWidth, ...item.style }"
        ></el-input>
      </el-form-item>
      <el-form-item class="search-btn">
        <el-button type="primary" icon="el-icon-search" @click="handleSearch"
          >查询</el-button
        >
        <el-button @click="handleReset" icon="el-icon-refresh-right"
          >重置</el-button
        >
      </el-form-item>
    </el-form>

    <!-- title 和 工具栏 -->
    <div class="head" v-if="!hideTitleBar">
      <slot name="title">
        <span class="title">{{ title }}</span>
      </slot>
      <div class="toolbar">
        <slot name="toolbar"></slot>
      </div>
    </div>
    <!-- table表格栏 -->
    <div class="table">
      <el-table
        v-loading="loading"
        :data="tableData"
        :row-key="rowKey"
        tooltip-effect="dark"
        stripe
        :border="border"
        @selection-change="handleSelectionChange"
      >
        <el-table-column
          v-for="item in columns"
          :key="item.label"
          :type="item.type"
          :label="item.label"
          :prop="item.prop"
          :sortable="item.sortable"
          :filters="item.filters"
          :filter-method="item.filters && filterHandler"
          show-overflow-tooltip
          :width="item.width"
          :min-width="item.minWidth"
          :align="item.align"
          :fixed="item.fixed"
        >
          <template #header="scope" v-if="!!item.labelSlot">
            <slot :name="item.labelSlot" v-bind="scope"></slot>
          </template>
          <template #default="scope" v-if="!!item.tdSlot">
            <slot :name="item.tdSlot" v-bind="scope"></slot>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 分页 -->
    <el-pagination
      v-if="paginationConfig.show && total > 0"
      class="pagination"
      :style="paginationConfig.style"
      @size-change="handleSizeChange"
      :current-page.sync="pageNum"
      @current-change="handleCurrentChange"
      :page-sizes="paginationConfig.pageSizes"
      :page-size.sync="pageSize"
      :layout="paginationConfig.layout"
      :total="total"
    ></el-pagination>
  </div>
</template>
<script>
const getSearchModel = (search) => {
  const searchModel = {};
  if (search && search.fields) {
    search.fields.forEach((item) => {
      switch (item.type) {
        case "checkbox":
        case "checkbox-button":
          searchModel[item.name] = [];
          break;
        default:
          break;
      }
      if (item.defaultValue !== undefined) {
        searchModel[item.name] = item.defaultValue;
        // 日期范围和时间范围真实变量默认值
        if (
          (item.type === "daterange" || item.type === "datetimerange") &&
          !!item.trueNames &&
          Array.isArray(item.defaultValue)
        ) {
          item.defaultValue.forEach((val, index) => {
            searchModel[item.trueNames[index]] = val;
          });
        }
      }
    });
  }
  return searchModel;
};
export default {
  props: {
    // 请求数据的方法
    request: {
      type: Function,
    },
    // 表格标题
    title: {
      type: String,
      default: "",
    },
    // 是否隐藏标题栏
    hideTitleBar: {
      type: Boolean,
      default: false,
    },
    // 搜索表单配置，false表示不显示搜索表单
    search: {
      type: [Boolean, Object],
      default: false,
    },
    border: {
      type: Boolean,
      default: false,
    },
    // 表头配置
    columns: {
      type: Array,
      default: function (params) {
        return [];
      },
    },
    // 行数据的Key，同elementUI的table组件的row-key
    rowKey: {
      type: String,
      default: "id",
    },
    // 分页配置，false表示不显示分页
    pagination: {
      type: [Boolean, Object],
      default: () => ({}),
    },
  },
  components: {},
  data() {
    let paginationConfig = {
      show: false,
    };
    if (typeof this.pagination === "object") {
      const { layout, pageSizes, style } = this.pagination;
      paginationConfig = {
        show: true,
        layout: layout || "total, sizes, prev, pager, next, jumper",
        pageSizes: pageSizes || [10, 20, 30, 40, 50, 100],
        style: style || {},
      };
    }

    return {
      searchModel: getSearchModel(this.search),
      loading: false,
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: (!!this.pagination && this.pagination.pageSize) || 10,
      paginationConfig,
    };
  },
  created() {
    // 请求列表数据
    this.getTableData();
  },
  methods: {
    // 请求列表数据
    async getTableData() {
      this.loading = true;
      const searchModel = this.optimizeFields(this.search);
      const { data, total } = await this.request({
        pageNum: this.pageNum,
        pageSize: this.pageSize,
        ...searchModel,
      });
      this.loading = false;
      this.tableData = data;
      this.total = total;
    },
    // 搜索
    handleSearch() {
      this.pageNum = 1;
      this.getTableData();
    },
    // 重置函数
    handleReset() {
      if (JSON.stringify(this.searchModel) === "{}") {
        return;
      }
      this.pageNum = 1;
      this.searchModel = getSearchModel(this.search);
      this.getTableData();
    },
    // 刷新
    refresh() {
      this.getTableData();
    },
    // 优化搜索字段，
    // 1、如果搜索配置有transform处理函数，执行transform
    // 2、删除日期范围默认的name字段
    optimizeFields(search) {
      const searchModel = JSON.parse(JSON.stringify(this.searchModel));
      if (search && search.fields) {
        search.fields.forEach((item) => {
          if (!searchModel.hasOwnProperty(item.name)) {
            return;
          }
          if (!!item.transform) {
            searchModel[item.name] = item.transform(searchModel[item.name]);
          }
          if (
            (item.type === "daterange" || item.type === "datetimerange") &&
            !!item.trueNames
          ) {
            delete searchModel[item.name];
          }
        });
      }
      return searchModel;
    },
    // 当前页变化
    handleCurrentChange(page) {
      this.getTableData();
    },
    // 改变每页size数量
    handleSizeChange(value) {
      this.pageNum = 1;
      this.getTableData();
    },
    // 全选
    handleSelectionChange(arr) {
      this.$emit("selectionChange", arr);
    },
    // 过滤方法
    filterHandler(value, row, column) {
      const property = column["property"];
      return row[property] === value;
    },
    // 日期范围
    handleDateChange(value, item) {
      if (!item.trueNames) {
        return;
      }

      if (!!value) {
        value.forEach((val, index) => {
          this.searchModel[item.trueNames[index]] = val;
        });
      } else {
        item.trueNames.forEach((key) => {
          delete this.searchModel[key];
        });
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.page-box {
  width: 100%;
  box-sizing: border-box;
  .search {
    padding: 20px 20px 0;
    background: #fff;
    margin-bottom: 10px;
    display: flex;
    flex-wrap: wrap;
    .el-form-item {
      margin-bottom: 20px;
    }
    .search-btn {
      margin-left: auto;
    }
    ::v-deep {
      .el-input-number .el-input__inner {
        text-align: left;
      }
    }
  }
  .head {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px 20px 0;
    background: #fff;
    .title {
      font-size: 16px;
    }
  }
  .table {
    padding: 20px;
    background: #fff;
    ::v-deep th {
      background: #f6f6f6;
      color: rgba(0, 0, 0, 0.85);
    }
  }
  .pagination {
    padding: 0 20px 20px;
    background: #fff;
    text-align: right;
    :last-child {
      margin-right: 0;
    }
  }
}
</style>

