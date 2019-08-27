<template>
  <a-card :bordered="false">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="48">
          <a-col :md="8" :sm="24">
            <a-form-item label="资源池名称">
              <a-input v-model="queryParam.dbPoolName" placeholder />
            </a-form-item>
          </a-col>
          <a-col :md="8" :sm="24">
            <a-form-item label="数据库类型">
              <a-select v-model="queryParam.dbType" placeholder="请选择" default-value="-1">
                <a-select-option value="-1">全部</a-select-option>
                <a-select-option value="0">MySql</a-select-option>
                <a-select-option value="1">SqlServer</a-select-option>
                <a-select-option value="2">Oracle</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="!advanced && 8 || 24" :sm="24">
            <span
              class="table-page-search-submitButtons"
              :style="advanced && { float: 'right', overflow: 'hidden' } || {} "
            >
              <a-button type="primary" @click="handleSearch">查询</a-button>
              <a-button style="margin-left: 8px" @click="resetQueryParam">重置</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <div class="table-operator">
      <a-button type="primary" icon="plus" @click="$refs.createDatabasePool.add()">新建资源池</a-button>
      <a-button type="primary" icon="plus" @click="$refs.createDbConnection.add()">新建数据库</a-button>
      <a-button type="dashed" @click="tableOption">{{ optionAlertShow && '关闭' || '开启' }} alert</a-button>
    </div>

    <s-table
      ref="table"
      size="default"
      rowKey="key"
      :columns="columns"
      :data="loadData"
      :alert="options.alert"
      :rowSelection="options.rowSelection"
    >
      <span slot="serial" slot-scope="text, record, index">{{ index + 1 }}</span>
      <span slot="action" slot-scope="text, record">
        <template>
          <a @click="handleEdit(record)">编辑</a>
          <a-divider type="vertical" />
          <a @click="handleDelete(record)">删除</a>
        </template>
      </span>
    </s-table>
    <CreateDbConnection ref="createDbConnection" @ok="handleOk" />
    <CreateDatabasePool ref="createDatabasePool" @ok="handleOk" />
    <step-by-step-modal ref="modal" @ok="handleOk" />
  </a-card>
</template>

<script>
import moment from "moment";
import { STable } from "@/components";
import { getDatabasePoolList } from "@/api/manage";
import CreateDbConnection from "./modules/CreateDbConnection";
import CreateDatabasePool from "./modules/CreateDatabasePool";
const dbTypeMap = {
  0: {
    text: "MySql"
  },
  1: {
    text: "SqlServer"
  },
  2: {
    text: "Oracle"
  },
  3: {
    text: "MongoDb"
  }
};

export default {
  name: "TableList",
  components: {
    STable,
    CreateDatabasePool,
    CreateDbConnection
  },
  data() {
    return {
      mdl: {},
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 表头
      columns: [
        { title: "资源池名称", dataIndex: "dbPoolName" },
        {
          title: "数据库类型",
          dataIndex: "dbType",
          customRender: this.dbTypeConverter
        },
        { title: "资源池地址", dataIndex: "dbAddress" },
        { title: "描述", dataIndex: "description" },
        { title: "创建人", dataIndex: "createUser", sorter: true },
        { title: "创建时间", dataIndex: "createTime", sorter: true },
        { title: "修改人", dataIndex: "updateUser" },
        { title: "修改时间", dataIndex: "updateTime" },
        {
          title: "操作",
          dataIndex: "action",
          width: "150px",
          scopedSlots: { customRender: "action" }
        }
      ],
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        console.log("loadData.parameter", parameter);
        return getDatabasePoolList(
          Object.assign(parameter, this.queryParam)
        ).then(res => {
          return res.result;
        });
      },
      selectedRowKeys: [],
      selectedRows: [],

      // custom table alert & rowSelection
      options: {
        alert: {
          show: true,
          clear: () => {
            this.selectedRowKeys = [];
          }
        },
        rowSelection: {
          selectedRowKeys: this.selectedRowKeys,
          onChange: this.onSelectChange
        }
      },
      optionAlertShow: true
    };
  },
  created() {
    this.tableOption();
    getRoleList({ t: new Date() });
  },
  methods: {
    dbTypeConverter(value, row, index) {
      return dbTypeMap[value].text;
    },
    tableOption() {
      if (!this.optionAlertShow) {
        this.options = {
          alert: {
            show: true,
            clear: () => {
              this.selectedRowKeys = [];
            }
          },
          rowSelection: {
            selectedRowKeys: this.selectedRowKeys,
            onChange: this.onSelectChange
          }
        };
        this.optionAlertShow = true;
      } else {
        this.options = {
          alert: false,
          rowSelection: null
        };
        this.optionAlertShow = false;
      }
    },
    handleCreate() {
      this.$refs.createModal.add();
    },
    handleSearch() {
      this.$refs.table.refresh(true);
    },
    handleEdit(record) {
      console.log(record);
      this.$refs.modal.edit(record);
    },
    handleDelete(record) {
      //  loadData: parameter => {
      //   console.log("loadData.parameter", parameter);
      //   return deleteDatabasePool(
      //     Object.assign(parameter, this.queryParam)
      //   ).then(res => {
      //     return res.result;
      //   });
      this.$message.info("删除：" + record.dbPoolName);
    },
    handleOk() {
      //this.$refs.table.refresh();
    },
    onSelectChange(selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys;
      this.selectedRows = selectedRows;
    },
    toggleAdvanced() {
      this.advanced = !this.advanced;
    },
    resetQueryParam() {
      this.queryParam = {};
    }
  }
};
</script>
