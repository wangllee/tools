<template>
  <div>
    <a-table :columns="inputColumns" :dataSource="dataInput" :pagination="false" bordered>
      <template slot="dataType" slot-scope="text, record">
        <div v-if="editable">
          <a-select style="width:150px" :default-value="text" @change="e => handleChangeInput(e, record.key, 'dataType')">
            <a-select-option v-for="dataType in dataTypes" :key="dataType.index" :value="dataType.index">{{dataType.value}} </a-select-option>
          </a-select>
        </div>
        <div v-else>{{dataTypeConverter(text)}}</div>
      </template>
      <template slot="description" slot-scope="text, record">
        <div v-if="editable">
          <a-input :value="text" :disabled="editEnabled(record)" @change="e => handleChangeInput(e.target.value, record.key, 'description')" />
        </div>
        <div v-else>{{dataTypeConverter(text)}}</div>
      </template>
      <template slot="sampleValue" slot-scope="text, record">
        <div v-if="editable" class="ant-input-number-auto-size">
          <a-input-number :default-value="convertToNumber(text)" v-if="dataTypeConverter(record.dataType)=='number'"
            @change="e => handleChangeInput(e, record.key, 'sampleValue','number')" width="auto" />
          <a-input :value="text" v-else :disabled="editEnabled(record)" @change="e => handleChangeInput(e.target.value, record.key, 'sampleValue')" />
        </div>
        <div v-else>{{text}}</div>
      </template>
      <template slot="operation" slot-scope="text, record">
        <div class='editable-row-operations'>
          <a @click="() => addInput(record)" :disabled="!editEnabled(record)">添加</a>
          <a @click="() => deleteInput(record.key)" :disabled="!deleteEnabled(record)">删除</a>
        </div>
      </template>
    </a-table>

    <a-table :columns="outputColumns" :dataSource="dataOutput" :pagination="false" bordered>
      <template slot="dataType" slot-scope="text, record">
        <div v-if="editable">
          <a-select style="width:150px" :default-value="text" @change="e => handleChangeOutput(e, record.key, 'dataType')">
            <a-select-option v-for="dataType in dataTypes" :key="dataType.index" :value="dataType.index">{{dataType.value}} </a-select-option>
          </a-select>
        </div>
        <div v-else>{{dataTypeConverter(text)}}</div>
      </template>
      <template slot="description" slot-scope="text, record">
        <div v-if="editable">
          <a-input :value="text" :disabled="editEnabled(record)" @change="e => handleChangeOutput(e.target.value, record.key, 'description')" />
        </div>
        <div v-else>{{dataTypeConverter(text)}}</div>
      </template>
      <template slot="sampleValue" slot-scope="text, record">
        <div v-if="editable" class="ant-input-number-auto-size">
          <a-input-number :default-value="convertToNumber(text)" v-if="dataTypeConverter(record.dataType)=='number'"
            @change="e => handleChangeOutput(e, record.key, 'sampleValue','number')" class="ant-input-number-auto-size" />
          <a-input :value="text" v-else :disabled="editEnabled(record)"
            @change="e => handleChangeOutput(e.target.value, record.key, 'sampleValue')" />
        </div>
        <div v-else>{{text}}</div>
      </template>
      <template slot="operation" slot-scope="text, record">
        <div class='editable-row-operations'>
          <a @click="() => addOutput(record)" :disabled="!editEnabled(record)">添加</a>
          <a @click="() => deleteOutput(record.key)" :disabled="!deleteEnabled(record)">删除</a>
        </div>
      </template>
    </a-table>
  </div>
</template>

<script>
import moment from 'moment'
import enums from '@/utils/helper/enum'

const dataTypes = [{ index: 0, value: 'string' }, { index: 1, value: 'number' }, { index: 2, value: 'object' }, { index: 3, value: 'array' }]
var outputSource =
  '{"code":"0001","message":"OK","data":{"Table1":[{"birth":"1989-08-21T13:06:57.4240473","name":"king","age":30}],"Table2":[{"birth":"1989-08-21T13:06:57.4240473","name":"king","age":30},{"birth":"1991-08-21T13:06:57.4270603","name":"queen","age":28},{"birth":"2013-08-21T13:06:57.4270603","name":"prince","age":6}]}}'
//'{"Table1":[],"Table2":[]}'
//'{"$schema":"http://json-schema.org/draft-04/schema#","type":"object","properties":{"code":{"type":"number"},"message":{"type":"string"},"data":{"type":"array","items":{"type":"object","properties":{"Id":{"type":"string"},"ResourceName":{"type":"string"},"InvokeType":{"type":"string"},"UrlFirst":{"type":"string"},"UrlSecond":{"type":"string"},"UrlThird":{"type":"string"},"OutModel":{"type":"string"},"Description":{"type":"string"},"CreateUser":{"type":"string"},"CreateTime":{"type":"string"},"ApiResourcePoolInputs":{"type":"array","items":{"type":"object","properties":{"Id":{"type":"string"},"InputName":{"type":"string"},"InputModel":{"type":"string"},"InputPosition":{"type":"string"},"IsArray":{"type":"number"}}}}}}}}}'
//'{"type":"object","title":"empty object","properties":{"field_1":{"type":"string"},"field_2":{"type":"string"},"field_3_node":{"type":"object","properties":{"field_4":{"type":"string"},"field_5":{"type":"array","items":{"type":"object","properties":{"field_6":{"type":"string"},"field_7_node":{"type":"array","items":{"type":"string"}}},"required":["field_6","field_7_node"]}}},"required":["field_4","field_5"]}},"required":["field_1","field_2","field_3_node"]}'
//'{"type":"object","title":"empty object","properties":{"field_9":{"type":"string"},"field_13":{"type":"array","items":{"type":"object","properties":{"field_14":{"type":"string"},"field_15":{"type":"string"}}}},"field_12":{"type":"array","items":{"type":"array","items":{"type":"string"}}},"field_11":{"type":"string"},"field_10":{"type":"string"}}}'
const inputColumns = [
  { title: '参数名称', dataIndex: 'name', scopedSlots: { customRender: 'name' } },
  { title: '参数类型', dataIndex: 'dataType', width: '20%', scopedSlots: { customRender: 'dataType' } },
  { title: '参数示例', dataIndex: 'sampleValue', scopedSlots: { customRender: 'sampleValue' } },
  { title: '说明', dataIndex: 'description', scopedSlots: { customRender: 'description' } },
  // { title: '操作', dataIndex: 'operation', scopedSlots: { customRender: 'operation' } },
]

const outputColumns = [
  { title: '参数名称', dataIndex: 'name', scopedSlots: { customRender: 'name' } },
  { title: '参数类型', dataIndex: 'dataType', width: '20%', scopedSlots: { customRender: 'dataType' } },
  { title: '结果示例', dataIndex: 'sampleValue', scopedSlots: { customRender: 'sampleValue' } },
  { title: '说明', dataIndex: 'description', scopedSlots: { customRender: 'description' } },
  // { title: '操作', dataIndex: 'operation', scopedSlots: { customRender: 'operation' } },
]

const dataInput = [
  {
    key: 'root',
    name: `Edrward`,
    dataType: 2,
    description: '',
    children: new Array(),
  },
]
for (let i = 0; i < 4; i++) {
  dataInput[0].children.push({
    key: i.toString(),
    name: `Edrward ${i}`,
    dataType: 0,
    sampleValue: `London Park no. ${i}`,
    description: '',
  })
}
const dataOutput = []
const disabledKeys = ['array', 'object']

export default {
  data() {
    this.cacheDataInput = dataInput.map((item) => ({ ...item }))
    this.cacheDataOutput = dataOutput.map((item) => ({ ...item }))
    return {
      dataInput,
      dataOutput,
      inputColumns,
      outputColumns,
      dataTypes,
      itemKey: 0,
      editable: true,
    }
  },
  created() {
    //this.jsonSchemaToOutputData()
    //this.outputDataToJsonSchema()

    var generateSchema = require('generate-schema')
    console.info(generateSchema.json(JSON.parse(outputSource)))
    this.objectToOutputData()
    this.outputDataToJsonSchema()
  },
  methods: {
    jsonSchemaToOutputData() {
      var jsonObj = JSON.parse(outputSource)
      console.info(jsonObj)
      var nodeType = Reflect.get(jsonObj, 'type')
      var title = Reflect.get(jsonObj, 'title')
      console.info(nodeType)
      console.info(title)
      var root = { key: 'root', name: title, dataType: 2, children: new Array() }
      this.setDataFromJsonSchema(jsonObj, root)
      console.info(root)
      dataOutput.push(root)
    },
    setDataFromJsonSchema(jsonObj, parent) {
      //console.info(jsonObj)
      var nodeType = Reflect.get(jsonObj, 'type')
      if (nodeType == 'object' && Reflect.has(jsonObj, 'properties')) {
        parent.dataType = 3
        parent.children = new Array()
        var nodeProperties = Reflect.get(jsonObj, 'properties')
        Reflect.ownKeys(nodeProperties).forEach((item) => {
          var itemValue = Reflect.get(nodeProperties, item)
          var child = { key: this.itemKey++, name: item }
          this.setDataFromJsonSchema(itemValue, child)
          parent.children.push(child)
        })
      } else if (nodeType == 'array' && Reflect.has(jsonObj, 'items')) {
        parent.dataType = 4
        parent.children = new Array()
        var nodeProperties = Reflect.get(jsonObj, 'items')
        var child = { key: this.itemKey++, name: 'items' }
        this.setDataFromJsonSchema(nodeProperties, child)
        parent.children.push(child)
      } else {
        parent.dataType = 0
      }
    },
    outputDataToJsonSchema() {
      var jsonObj = new Object()
      if (this.dataOutput.length > 0) {
        jsonObj.type = this.dataTypeConverter(this.dataOutput[0].dataType)
        jsonObj.title = this.dataOutput[0].name
        //jsonObj.properties = new Object()
        this.getJsonSchemaFromData(this.dataOutput[0].children, jsonObj)
        console.info(jsonObj)
        console.info(JSON.stringify(jsonObj))
      }
    },
    getJsonSchemaFromData(data, jsonObj) {
      if (data) {
        if (jsonObj.type == 'object') {
          jsonObj.properties = new Object()
          for (var i = 0; i < data.length; i++) {
            var obj = new Object()
            Reflect.set(jsonObj.properties, data[i].name, obj)
            var nodeType = this.dataTypeConverter(data[i].dataType)
            if (nodeType == 'object') {
              obj.type = 'object'
              this.getJsonSchemaFromData(data[i].children, obj)
            } else if (nodeType == 'array') {
              obj.type = 'array'
              this.getJsonSchemaFromData(data[i].children, obj)
            } else {
              obj.type = 'string'
            }
          }
        } else {
          jsonObj.items = new Object()
          for (var i = 0; i < data.length; i++) {
            var nodeType = this.dataTypeConverter(data[i].dataType)
            if (nodeType == 'object') {
              jsonObj.items.type = 'object'
              this.getJsonSchemaFromData(data[i].children, jsonObj.items)
            } else if (nodeType == 'array') {
              jsonObj.items.type = 'array'
              this.getJsonSchemaFromData(data[i].children, jsonObj.items)
            } else {
              jsonObj.items.type = 'string'
            }
          }
        }
      }
    },

    editEnabled(record) {
      if (this.editable) {
        var typeName = this.dataTypeConverter(record.dataType)
        return disabledKeys.indexOf(typeName) > -1
      } else {
        return false
      }
    },
    deleteEnabled(record) {
      if (this.editable && record.key != 'root') {
        return true
      } else {
        return false
      }
    },
    objectToOutputData() {
      var jsonObj = JSON.parse(outputSource)
      var root = { key: 'root', name: 'root', dataType: 2, children: new Array() }
      this.setChild(jsonObj, root)
      console.info(root)
      //dataOutput.push(...root.children)
      dataOutput.push(root)
    },
    setChild(jsonObj, parent) {
      if (typeof jsonObj == 'string') return
      Reflect.ownKeys(jsonObj).forEach((item) => {
        var propertyValue = Reflect.get(jsonObj, item)
        if (typeof propertyValue == 'object') {
          if (propertyValue instanceof Array) {
            var arrayChild = { key: this.itemKey++, name: item, dataType: 3, children: new Array() }
            if (propertyValue.length > 0) {
              var child = { key: this.itemKey++, name: 'items', dataType: 2, children: new Array() }
              this.setChild(propertyValue[0], child)
              arrayChild.children.push(child)
            }
            parent.children.push(arrayChild)
          } else {
            var child = { key: this.itemKey++, name: item, dataType: 2, children: new Array() }
            this.setChild(propertyValue, child)
            parent.children.push(child)
          }
        } else {
          var child = { key: this.itemKey++, name: item, dataType: 0, sampleValue: Reflect.get(jsonObj, item) }
          parent.children.push(child)
        }
      })
    },
    getChild(items, key) {
      for (var i = 0; i < items.length; i++) {
        if (items[i].key == key) {
          return items[i]
        } else if (items[i].children) {
          var child = this.getChild(items[i].children, key)
          if (child) {
            return child
          }
        }
      }
      return undefined
    },
    moment,
    dataTypeConverter(index) {
      return this.dataTypes[index].value
    },
    handleChangeInput(value, key, column, dataType) {
      console.info(key)
      console.info(column)
      console.info(value)
      const newData = [...this.dataInput]
      const target = this.getChild(newData, key)
      if (target) {
        if (column == 'dataType' && disabledKeys.indexOf(dataType) > -1) {
          target['sampleValue'] = ''
        }
        target[column] = value
        this.dataInput = newData
      }
    },

    deleteInput(key) {
      const newData = [...this.dataInput]
      this.deleteItem(this.dataInput, key)
      this.dataInput = newData
    },
    addInput(record) {
      const newData = [...this.dataInput]
      this.addItem(record)
      this.dataInput = newData
    },
    deleteOutput(key) {
      const newData = [...this.dataOutput]
      this.deleteItem(this.dataOutput, key)
      this.dataOutput = newData
    },
    addOutput(record) {
      const newData = [...this.dataOutput]
      this.addItem(record)
      this.dataOutput = newData
    },
    addItem(record) {
      if (!record.children) {
        record.children = new Array()
      }
      record.children.push({ key: this.itemKey++, name: 'item', dataType: 0 })
    },

    deleteItem(items, key) {
      for (var i = 0; i < items.length; i++) {
        if (items[i].key == key) {
          var item = items.splice(i, 1)
          console.info(item)
        } else if (items[i].children) {
          this.deleteItem(items[i].children, key)
        }
      }
    },
    handleChangeOutput(value, key, column, dataType) {
      console.info(key)
      console.info(column)
      if (dataType == 'datetime') {
        value = this.convertToDateTime(value)
      }
      console.info(value)
      const newData = [...this.dataOutput]
      const target = this.getChild(newData, key)
      if (target) {
        target[column] = value
        this.dataOutput = newData
        if (column == 'dataType') {
        }
      }
    },

    valueConverter(target) {
      var dataType = this.dataTypeConverter(target.dataType)
      if (dataType == 'datetime') {
        target['sampleValue'] = this.convertToDateTime(target['sampleValue'])
      } else if (dataType == 'number') {
        target['sampleValue'] = this.convertToNumber(target['sampleValue'])
      }
      console.info(target['sampleValue'])
    },
    convertToNumber(text) {
      var result = parseInt(text)
      return isNaN(result) ? 0 : result
    },
    convertToDateTime(text) {
      return moment(text).format('YYYY-MM-DD HH:mm:ss')
    },
  },
}
</script>
<style scoped>
.editable-row-operations a {
  margin-right: 8px;
}
.ant-input-number-auto-size >>> .ant-input-number {
  width: auto;
}
</style>
