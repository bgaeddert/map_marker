<template>
  <el-tabs v-model="activeName" class="demo-tabs">
    <el-tab-pane label="Positions" name="list">
      <detail-list
          v-bind="$attrs"
          :pos-data="posData"
      />
    </el-tab-pane>
    <el-tab-pane label="Preview" name="preview">
      <div v-if="!currentPreview"><h3>Select a marker on the map...</h3></div>

      <div v-if="currentPreview">
        <div v-if="!currentPreview.imageLink">
          <el-skeleton style="width: 240px">
            <template #template>
              <el-skeleton-item variant="image" style="width: 240px; height: 240px"/>
            </template>
          </el-skeleton>
        </div>

        <div v-if="currentPreview.imageLink">
          <el-card :body-style="{ padding: '0px' }">
            <el-image
                :src="currentPreview.imageLink"
                class="image"
            />
          </el-card>
        </div>

        <position-block
            v-bind="$attrs"
            :idx="currentPreview.idx"
            :title="currentPreview.title"
            :latitude="currentPreview.latitude"
            :longitude="currentPreview.longitude"
            :link="currentPreview.link"
            :image-link="currentPreview.imageLink"
            :type="currentPreview.type"
        ></position-block>
      </div>
    </el-tab-pane>
    <el-tab-pane label="Nearest" name="nearest">
      <h3>{{ (boundedLocations || []).length }} Locations</h3>

      <div class="position-con" ref="boundCon">
        <div class="position-item"
             v-for="(item, i) in boundedLocations"
             v-bind:key="i"
             ref="boundItems"
             :class="{ 'alert-bg': alertBackground === item.posIdx }"
        >
          <preview-block
              v-bind="$attrs"
              :idx="item.posIdx"
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
import TypeBlock from "@/components/TypeBlock";
import PreviewBlock from "@/components/PreviewBlock";
import DetailList from "@/components/DetailList";
import PositionBlock from "@/components/PositionBlock";

export default {
  name: "ControlBlock",
  components: {
    DetailList,
    PreviewBlock,
    JsonDataBlock,
    TypeBlock,
    PositionBlock,
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
      currentPreview: null,
    }
  },
  watch: {
    scrollRefIndex: {
      handler(index) {
        if(!this.posData[index]){
          return null;
        }

        this.currentPreview = {...this.posData[index], idx: index};

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

.demo-tabs > .el-tabs__content {
  padding: 5px;
  color: #6b778c;
}
</style>