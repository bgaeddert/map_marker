<template>
  <el-row :gutter="10">
    <el-col :span="24">
      <el-button @click="handleUpdate">Update</el-button>
    </el-col>
  </el-row>
  <el-row :gutter="10">
    <el-col :span="24">
      <JsonEditorVue v-model="jsonData" v-bind="{/* local config */}"/>
    </el-col>
  </el-row>
</template>

<script>
import JsonEditorVue from 'json-editor-vue'

export default {
  name: "JsonDataBlock",
  components: {
    JsonEditorVue,
  },
  props: {
    posData: Array
  },
  methods: {
    handleUpdate: function () {
      this.$emit("update-properties", this.jsonData);
    }
  },
  data() {
    return {
      jsonData: this.posData,
    }
  },
  watch: {
    posData: {
      handler(newData) {
        this.jsonData = newData;
      },
      immediate: true
    }
  },
  emits: [
    'add-property',
    'remove-properties',
    'updateProperty',
    'updateProperties'
  ]
}
</script>

<style scoped>
</style>