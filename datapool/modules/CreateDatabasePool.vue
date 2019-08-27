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
        <a-form-item label="资源池名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input></a-input>
        </a-form-item>
        <a-form-item label="服务器地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input></a-input>
        </a-form-item>
        <a-form-item label="数据库类型" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-select default-value="0">
            <a-select-option value="0">MySql</a-select-option>
            <a-select-option value="1">SqlServer</a-select-option>
            <a-select-option value="2">Oracle</a-select-option>
          </a-select>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
export default {
  data() {
    return {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 }
      },
      visible: false,
      form: this.$form.createForm(this),
      confirmLoading: false
    };
  },
  methods: {
    add() {
      this.visible = true;
    },
    handleSubmit() {
      this.visible = false;
    },
    handleCancel() {
      this.visible = false;
    }
  }
};
</script>

<style lang="scss" scoped>
</style>