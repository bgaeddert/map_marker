<template>
  <div id="content">
    <el-row :gutter="10">
      <el-col :span="24">
        <el-button @click="handleUpdate">Update</el-button>
      </el-col>
    </el-row>
    <div v-for="(typeConfig, i) in liveConfigs"
         ref="items"
         v-bind:key="i">
      <div class="config-con">
        <el-row :gutter="10">
          <el-col :span="24">
            <h4>{{typeConfig.typeName}}</h4>
          </el-col>
        </el-row>
        <el-row :gutter="10">
          <el-col :span="24">
            <el-color-picker v-model="typeConfig.markerColor" @change="handleChange(i, typeConfig)"/>
          </el-col>
        </el-row>
      </div>
      <el-divider/>
    </div>
  </div>
</template>

<script>
export default {
  name: "TypeBlock",
  props: {
    posData: Array,
  },
  data() {
    return {
      typeConfigs: [],
      liveConfigs: this.posData.reduce((acc, m) => {
        const temp = acc.find((t) => t.typeName === m.type);

        if (!temp) {
          acc.push({
            typeName: m.type,
            markerColor: '#d75050'
          });
        }

        return acc
      }, []),
    }
  },
  methods: {
    saveData() {
      localStorage.setItem("typeConfigs", JSON.stringify(this.typeConfigs));
    },
    handleUpdate: function () {
      this.typeConfigs = this.liveConfigs;
      this.saveData();
      this.$emit("update-configs", this.typeConfigs);
    },
    handleChange: function (index, typeConfig) {
      this.liveConfigs[index] = typeConfig;
    }
  },
  mounted() {
    this.typeConfigs = JSON.parse(localStorage.getItem("typeConfigs")) || [];
  },
  watch: {
    posData: {
      handler() {
        let typeConfigs = JSON.parse(localStorage.getItem("typeConfigs")) || [];

        this.liveConfigs = this.posData.reduce((acc, m) => {
          const temp = acc.find((t) => t.typeName === m.type);
          const typeConfig = typeConfigs.find((t) => t.typeName === m.type);

          if (!temp) {
            if (typeConfig) {
              acc.push(typeConfig);
            } else {
              acc.push({
                typeName: m.type,
                markerColor: '#d75050'
              });
            }
          }

          return acc
        }, [])
      },
      immediate: true
    },
  },
  emits: [
      'update-configs',
  ]
}
</script>

<style scoped>
.config-con{
  padding: 10px;
  border: 1px solid #bdbcbc;
  border-radius: 6px;
}
</style>