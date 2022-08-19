<template>
  <el-card :body-style="{ padding: '0px' }">
    <el-image
        :src="imageLink"
        class="image"
        @click="handleCenter"
    />
    <div style="padding: 14px">
      <h3>{{ title }}</h3>
      <el-row>
        <el-col>
          <div v-for="(item, i) in details" v-bind:key="i">{{ item.label }}: {{ item.value }}</div>
        </el-col>
      </el-row>
    </div>
  </el-card>
</template>

<script>
export default {
  name: "PreviewBlock",
  methods: {
    handleCenter() {
      this.$emit("center-map", {lat: parseFloat(this.latitude), lng: parseFloat(this.longitude)}),
          this.$emit("scroll-pos", this.idx)
    }
  },
  props: {
    title: String,
    latitude: String,
    longitude: String,
    imageLink: String,
    link: String,
    type: String,
    details: Array,
    idx: Number,
  },
  emits: [
    'center-map',
    'scroll-pos'
  ],
}
</script>

<style scoped>

</style>