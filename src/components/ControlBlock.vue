<template>
  <el-tabs v-model="activeName" class="demo-tabs">
    <el-tab-pane label="Positions" name="list">
      <h3>{{ posData.length }} Locations</h3>

      <el-row class="row-bg" justify="center">
        <el-pagination background @current-change="handlePageChange" small layout="prev, pager, next"
                       :page-size="pageSize" current-page="currentPage" :total="posData.length"/>
      </el-row>

      <div class="position-con" ref="posCon">
        <div class="position-item"
             v-for="(item, i) in locations"
             ref="items"
             v-bind:key="i"
             :class="{ 'alert-bg': alertBackground === i }"
        >
          <position-block
              v-bind="$attrs"
              :index="i"
              :idx="i"
              :title="item.title"
              :latitude="item.latitude"
              :longitude="item.longitude"
              :link="item.link"
              :image-link="item.imageLink"
              :type="item.type"
          ></position-block>
        </div>

        <el-divider/>
      </div>
    </el-tab-pane>
    <el-tab-pane label="Preview" name="preview">
      <h3>{{ boundedLocations.length }} Locations</h3>

      <div class="position-con" ref="boundCon">
        <preview-block
            v-if="currentPreview"
            v-bind="$attrs"
            :idx="currentPreview.posIdx"
            :title="currentPreview.title"
            :latitude="currentPreview.latitude"
            :longitude="currentPreview.longitude"
            :link="currentPreview.link"
            :image-link="currentPreview.imageLink"
            :type="currentPreview.type"
            :details="currentPreview.details"
        ></preview-block>

        <div class="position-item"
             v-for="(item, i) in boundedLocations"
             v-bind:key="i"
             ref="boundItems"
             :class="{ 'alert-bg': alertBackground === item.posIdx }"
        >
          {{i}} - {{item.posIdx}}
          <preview-block
              v-bind="$attrs"
              :index="i"
              :idx="i"
              :title="item.title"
              :latitude="item.latitude"
              :longitude="item.longitude"
              :link="item.link"
              :image-link="item.imageLink"
              :type="item.type"
              :details="item.details"
          ></preview-block>
        </div>

        <el-divider/>
      </div>
    </el-tab-pane>
    <el-tab-pane label="Data" name="json">
      <json-data-block :pos-data="posData" v-bind="$attrs"></json-data-block>
    </el-tab-pane>
    <el-tab-pane label="Marker Config" name="start">
      <TypeBlock :pos-data="posData" v-bind="$attrs"/>
    </el-tab-pane>
  </el-tabs>
</template>

<script>
import JsonDataBlock from "@/components/JsonDataBlock";
import PositionBlock from "@/components/PositionBlock";
import TypeBlock from "@/components/TypeBlock";
import PreviewBlock from "@/components/PreviewBlock";

export default {
  name: "ControlBlock",
  components: {
    PreviewBlock,
    JsonDataBlock,
    PositionBlock,
    TypeBlock,
  },
  props: {
    posData: Array,
    scrollRefIndex: Number,
    boundedLocations: Array
  },
  data() {
    return {
      activeName: 'list',
      alertBackground: 0,
      currentPage: '',
      pageSize: 50,
      locations: this.posData.slice(0, this.pageSize - 1),
      currentPreview: null,
    }
  },
  watch: {
    posData: {
      handler() {
        this.handlePageChange(1)
      },
      immediate: true
    },
    scrollRefIndex: {
      handler(index) {
        console.log(index)

        this.currentPreview = this.posData[index];

        //this.alertBackground = -1;
        //if (!this.$refs.boundItems) {
        //  return;
        //}
        //
        //if (!this.$refs.boundCon) {
        //  return;
        //}
        //
        //var element = this.$refs.boundItems[index];
        //var posCon = this.$refs.boundCon;
        //
        //
        //if (!element) {
        //  return;
        //}
        //
        //this.alertBackground = index
        //var top = element.offsetTop;
        //posCon.scrollTo(0, top - 80);
      },
      immediate: true
    }
  },
  methods: {
    handlePageChange(page) {
      let iPage = page - 1;
      let start = iPage * this.pageSize;
      let end = start + this.pageSize - 1

      this.locations = this.posData.slice(start, end);
    }
  },
}
</script>

<style>
.position-con {
  height: 800px;
  overflow-y: scroll;
}

.position-item {
  padding: 10px;
  border-radius: 6px;
}

.alert-bg {
  background: #ffffff;
  animation: fadeBackground 3s;
  animation-fill-mode: forwards;
}

@keyframes fadeBackground {
  from {
    background-color: #d0705a;
  }
  to {
    background-color: #ffffff;
  }
}

.demo-tabs > .el-tabs__content {
  padding: 5px;
  color: #6b778c;
}
</style>