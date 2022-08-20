<template>
  <el-form label-width="120px">
    <el-row :gutter="10">
      <el-col :span="24">
        <el-input v-model="propertyTitle" @input="handleChange" placeholder="Title"/>
      </el-col>
    </el-row>
    <el-row :gutter="10">
      <el-col :span="12">
        <el-input v-model="propertyLatitude" @input="handleChange" placeholder="Latitude" type="number" step="0.00001"/>
      </el-col>
      <el-col :span="12">
        <el-input v-model="propertyLongitude" @input="handleChange" placeholder="Longitude" type="number" step="0.00001"/>
      </el-col>
    </el-row>
    <el-row :gutter="10">
      <el-col :span="24">
        <el-input v-model="propertyLatLng" @input="handleLatLngChange" placeholder="Latitude, Longitude" type="text"/>
      </el-col>
    </el-row>
    <el-row :gutter="10">
      <el-col :span="24">
        <el-input v-model="propertyImageLink" @input="handleChange" placeholder="Thumbnail"/>
      </el-col>
    </el-row>
    <el-row :gutter="10">
      <el-col :span="24">
        <el-input v-model="propertyLink" @input="handleChange" placeholder="URL"/>
      </el-col>
    </el-row>
    <el-row :gutter="10">
      <el-col :span="12">
        <el-select v-model="propertyType" placeholder="Select" @change="handleChange()">
          <el-option
              v-for="item in typeOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
          />
        </el-select>
      </el-col>
      <el-col :span="12">
        <el-button @click="handleCenter">Center Here</el-button>
      </el-col>
    </el-row>
  </el-form>
</template>

<script>
export default {
  name: "PositionBlock",
  methods: {
    handleChange() {
      const updated = {
        title: this.propertyTitle,
        latitude: this.propertyLatitude,
        longitude: this.propertyLongitude,
        imageLink: this.propertyImageLink,
        link: this.propertyLink,
        type: this.propertyType
      }
      this.$emit("update-property", {
        idx: this.idx,
        property: updated
      })
    },
    handleLatLngChange() {
      let parts = this.propertyLatLng.split(',');

      if(parts.length !== 2){
        return;
      }

      let lat = (parts[0] || '').trim();
      let lng = (parts[1] || '').trim();

      this.propertyLatitude = lat;
      this.propertyLongitude = lng;

      this.handleChange();
    },
    handleCenter(){
      this.$emit("center-map", {lat: parseFloat(this.propertyLatitude), lng: parseFloat(this.propertyLongitude)})
    }
  },
  props: {
    title: String,
    latitude: String,
    longitude: String,
    imageLink: String,
    link: String,
    type: String,
    idx: Number,
  },
  data() {
    return {
      propertyTitle: this.title,
      propertyLatitude: this.latitude,
      propertyLongitude: this.longitude,
      propertyLatLng: this.latitude + ', ' + this.longitude,
      propertyImageLink: this.imageLink,
      propertyLink: this.link,
      propertyType: this.type,
      propertyIdx: this.idx,
      typeOptions: [
        {label: 'Store', value: 'store'},
        {label: 'Other', value: 'other'}
      ]
    }
  },
  emits: [
    'update-property',
    'center-map',
  ],
  watch: {
    title: {
      handler(newData) {
        this.propertyTitle = newData;
      },
      immediate: true
    },
    latitude: {
      handler(newData) {
        this.propertyLatitude = newData;
      },
      immediate: true
    },
    longitude: {
      handler(newData) {
        this.propertyLongitude = newData;
      },
      immediate: true
    },
    imageLink: {
      handler(newData) {
        this.propertyImageLink = newData;
      },
      immediate: true
    },
    link: {
      handler(newData) {
        this.propertyLink = newData;
      },
      immediate: true
    },
    type: {
      handler(newData) {
        this.propertyType = newData;
      },
      immediate: true
    }
  },
}
</script>

<style scoped>

</style>