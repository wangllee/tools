<template>
  <a-modal
    title="新建数据库资源池"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="handleCancel"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item label="资源池名称:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            :disabled="isReadonly"
            v-decorator="[ 'poolName', {  rules: [{ required: true, message: '资源池名称不能为空!' }] } ]"
          ></a-input>
        </a-form-item>
        <a-form-item label="数据库类型:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select
            default-value="0"
            v-decorator="[ 'dbType', { rules: [{ validator: checkServerAddress }], } ]"
          >
            <a-select-option value="0">MySql</a-select-option>
            <a-select-option value="1">SqlServer</a-select-option>
            <a-select-option value="2">Oracle</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item label="服务器地址:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            :disabled="isReadonly"
            v-decorator="[ 'serverAddress', { rules: [{ validator: checkServerAddress }], } ]"
          ></a-input>
        </a-form-item>
        <a-form-item label="服务名称:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            :disabled="isReadonly"
            v-decorator="[ 'serverName', { rules: [{ validator: checkServerAddress }], } ]"
          ></a-input>
        </a-form-item>
        <a-form-item label="端口号:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            :disabled="isReadonly"
            v-decorator="[ 'ipPort', { rules: [{ validator: checkServerAddress }], } ]"
          ></a-input>
        </a-form-item>
        <a-form-item label="数据库名称:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            :disabled="isReadonly"
            v-decorator="[ 'dbName', {  rules: [{ required: true, message: '数据库名称不能为空!' }] } ]"
          ></a-input>
        </a-form-item>
        <a-form-item label="用户名称:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            :disabled="isReadonly"
            v-decorator="[ 'userName', {  rules: [{ required: true, message: '用户名称不能为空!' }] } ]"
          ></a-input>
        </a-form-item>
        <a-form-item label="登录密码:" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input
            :disabled="isReadonly"
            v-decorator="[ 'record.Password', {  rules: [{ required: true, message: '登录密码不能为空!' }] } ]"
          ></a-input>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
export default {
  data() {
    return {
      record: {},
      isReadonly: false,
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 }
      },
      visible: false,
      confirmLoading: false,
      form: this.$form.createForm(this)
    };
  },
  methods: {
    add() {
      this.visible = true;
    },
    handleSubmit() {
      //e.preventDefault();
      this.form.validateFields((err, values) => {
        if (!err) {
          console.log("Received values of form: ", values);
        }
      });
      //this.visible = false;
      record = {};
    },
    handleCancel() {
      this.visible = false;
      record = {};
    }
  }
};
</script>

<style lang="scss" scoped>
</style>