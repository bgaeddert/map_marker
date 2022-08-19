<template>
  <div id="content">
    <el-row>
      <el-col>
        <h3>{{ location.title }}</h3>
      </el-col>
    </el-row>
    <!--<el-row>-->
    <!--  <el-col>-->
    <!--    <el-image :src="location.imageLink"/>-->
    <!--  </el-col>-->
    <!--</el-row>-->
    <el-row>
      <el-col>
        <a :href="location.link" target="_blank">More info...</a>
      </el-col>
    </el-row>
    <el-row>
      <el-col>
        Location: {{ location.latitude }}, {{ location.longitude }}
      </el-col>
    </el-row>
    <el-row>
      <el-col>
        <div v-for="(item, i) in location.details" v-bind:key="i">{{item.label}}: {{item.value}}</div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: "InfoContent",
  props:{
    location: Object,
  }
}
</script>

<style scoped>

</style>