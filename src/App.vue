<template>
  <div class="common-layout">
    <el-container>
      <el-header>MapMarker</el-header>
      <el-main>
        <el-row :gutter="20">
          <el-col :span="8">
            <control-block
                :pos-data="posData"
                :scroll-ref-index="refIdx"
                :bounded-locations="boundLocations"
                @update-property="handleUpdateProperty"
                @update-properties="handleUpdateProperties"
                @remove-properties="handleRemoveProperties"
                @add-property="handleAddProperty"
                @center-map="handleCenterMap"
                @update-configs="handleConfigUpdate"
            ></control-block>
          </el-col>
          <el-col :span="16">
            <google-map
                :pos-data="posData"
                :map-center="center"
                :type-configs="typeConfigs"
                @add-property="handleAddProperty"
                @remove-last-property="handleRemoveLastProperty"
                @update-last-property="handleUpdateLastProperty"
                @center-map="handleCenterMap"
                @scroll-pos="handleScrollPos"
                @update-bound-locations="handleBoundLocations"
            ></google-map>
          </el-col>
        </el-row>
      </el-main>
      <el-footer>By: Brian Gaeddert</el-footer>
    </el-container>
  </div>
</template>

<script>
import GoogleMap from "@/components/GoogleMap";
import ControlBlock from "@/components/ControlBlock";

export default {
  name: 'App',
  components: {
    ControlBlock,
    GoogleMap
  },
  methods: {
    handleUpdateProperty(event) {
      this.posData[event.idx] = event.property;
      this.saveData();
    },
    handleUpdateProperties(properties) {
      this.posData = properties;
      this.saveData();
    },
    handleAddProperty(property) {
      this.posData.push(property)
      this.saveData();
    },
    handleRemoveLastProperty() {
      this.posData = this.posData.slice(0, -1);
      this.saveData();
    },
    handleUpdateLastProperty(property) {
      this.posData[this.posData -1] = property;
      this.saveData();
    },
    handleRemoveProperties() {
      this.posData = [];
      this.saveData();
    },
    saveData() {
      localStorage.setItem("properties", JSON.stringify(this.posData));
    },
    handleCenterMap(pos){
      this.center = pos;
    },
    handleScrollPos(refIdx){
      this.refIdx = refIdx;
    },
    handleConfigUpdate(){
      this.typeConfigs = JSON.parse(localStorage.getItem("typeConfigs"));
    },
    handleBoundLocations(boundLocations){
      this.boundLocations = boundLocations;
    }
  },
  data() {
    return {
      activeName: 'list',
      posData: [],
      center: {lat: 6.199146, lng: -75.56712},
      refIdx: 0,
      typeConfigs: null,
      boundLocations: []
    }
  },
  mounted() {
      this.posData = JSON.parse(localStorage.getItem("properties"));
      this.typeConfigs = JSON.parse(localStorage.getItem("typeConfigs"));
  },
}
</script>

<style>
</style>
