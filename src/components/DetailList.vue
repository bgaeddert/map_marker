<template>
  <div>
    <h3>{{ totalLocations }} Locations</h3>

    <el-row class="row-bg" justify="center">
      <el-pagination background
                     small layout="prev, pager, next"
                     :page-size="pageSize"
                     :current-page="currentPage"
                     :total="totalLocations"
                     @current-change="handlePageChange"
      />
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
  </div>
</template>

<script>
import PositionBlock from "@/components/PositionBlock";

export default {
  name: "DetailList",
  components: {
    PositionBlock,
  },
  props: {
    posData: Array,
  },
  data() {
    return {
      pageSize: 10,
      currentPage: 1,
    }
  },
  computed: {
    totalLocations: function() {
      if (!this.posData) {
        return 0;
      }

      return this.posData.length
    },
    locations: function() {
      if(!this.posData){
        return [];
      }

      let iPage = this.currentPage - 1;
      let start = iPage * this.pageSize;
      let end = start + this.pageSize - 1

      return (this.posData || []).slice(start, end);
    }
  },
  methods: {
    handlePageChange(page) {
      this.currentPage = page;
    }
  }
}
</script>

<style scoped>
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
</style>