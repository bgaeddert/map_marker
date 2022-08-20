<template>
  <div>
    <div>
      <GoogleMap api-key="AIzaSyDO-pkAscwkFsapA-iZuaBQP8mYP_Orxrk"
                 ref="mapRef"
                 style="width: 100%; height: 500px;"
                 :center="mapCenter"
                 :zoom="15"
                 draggable-cursor="true"
                 :map-type-control="shouldMapControl"
                 :fullscreen-control="shouldFullScreen"
                 :street-view-control="shouldStreetView"
                 @rightclick="handleRightClick"
                 @bounds_changed="handleIdle"
      >
        <MarkerCluster v-if="shouldCluster">
          <Marker v-for="(location, i) in locations"
                  @click="handleMarkerClick(location.idx)"
                  :options="{ position: location.position, icon: pinSymbol(location.type) }"
                  :key="i"
          >
            <InfoWindow ref="otherInfoWindows"
                        @visible="handleInfoVisible('otherInfoWindows', i)"
                        @closeclick="handleCloseInfoWindow"
            >
              <info-content :location="location"/>
            </InfoWindow>
          </Marker>
        </MarkerCluster>

        <Marker v-for="(store, i) in stores"
                @click="handleMarkerClick(store.idx)"
                :options="{ position: store.position, icon: pinSymbol(store.type) }"
                :key="i"
        >
          <InfoWindow ref="storeInfoWindows"
                      @visible="handleInfoVisible('storeInfoWindows', i)"
                      @closeclick="handleCloseInfoWindow"
          >
            <info-content :location="store"/>
          </InfoWindow>
        </Marker>

        <div v-if="!shouldCluster">
          <Marker v-for="(location, i) in locations"
                  @click="handleMarkerClick(location.idx)"
                  :options="{ position: location.position, icon: pinSymbol(location.type) }"
                  :key="i"
          >
            <InfoWindow ref="clusteredOtherInfoWindows"
                        @visible="handleInfoVisible('clusteredOtherInfoWindows', i)"
                        @closeclick="handleCloseInfoWindow"
            >
              <info-content :location="location"/>
            </InfoWindow>
          </Marker>
        </div>
      </GoogleMap>

      <el-row>
        <el-col :span="12">
          <el-input v-model="center.lat" @input="handleCenterLat" placeholder="Latitude" type="number"
                    step="0.00001"/>
        </el-col>
        <el-col :span="12">
          <el-input v-model="center.lng" @input="handleCenterLng" placeholder="Longitude" type="number"
                    step="0.00001"/>
        </el-col>
      </el-row>

      <div>
        <div>
          <el-checkbox v-model="shouldCluster" label="Cluster Markers" size="large"/>
          <el-checkbox v-model="shouldMapControl" label="Map Type Control" size="large"/>
          <el-checkbox v-model="shouldFullScreen" label="Fullscreen Control" size="large"/>
          <el-checkbox v-model="shouldStreetView" label="Street view Control" size="large"/>
        </div>
      </div>

      <el-dialog v-model="centerDialogVisible" title="Add Marker?" width="30%" center>

        <el-row :gutter="10">
          <h3>So you want to add a marker ey...?</h3>
        </el-row>

        <el-row :gutter="10">
          <el-input v-model="nextProperty.title" placeholder="Price"/>
        </el-row>
        <template #footer>
      <span class="dialog-footer">
        <el-button @click="onRemoveLast">Cancel</el-button>
        <el-button type="primary" @click="onUpdateLast">Confirm</el-button>
      </span>
        </template>
      </el-dialog>
    </div>

  </div>
</template>

<script>
import {defineComponent} from "vue";
import {GoogleMap, Marker, InfoWindow, MarkerCluster} from "vue3-google-map";
import InfoContent from "@/components/InfoContent";

export default defineComponent({
  components: {InfoContent, GoogleMap, Marker, InfoWindow, MarkerCluster},
  props: {
    posData: Array,
    mapCenter: Object,
    typeConfigs: Array,
  },
  data() {
    return {
      lastInfoWindow: null,
      configsLoaded: false,
      centerDialogVisible: false,
      nextProperty: {},
      shouldCluster: true,
      shouldMapControl: false,
      shouldFullScreen: false,
      shouldStreetView: false,
      pinSymbol: function (type) {
        const typeConfig = this.typeConfigs.find((t) => t.typeName === type);

        let color = '#ea4335';
        if (typeConfig) {
          color = typeConfig.markerColor || color;
        }

        return {
          path: 'M 0,0 C -2,-20 -10,-22 -10,-30 A 10,10 0 1,1 10,-30 C 10,-22 2,-20 0,0 z',
          fillColor: color,
          fillOpacity: 1,
          strokeColor: '#000',
          strokeWeight: 2,
          scale: 1
        }
      }
    };
  },
  computed: {
    locations: {
      get() {
        if (!this.posData.length) {
          return []
        }

        return this.posData.map((m, i) => {
          return {...m, idx: i, position: {lat: parseFloat(m.latitude), lng: parseFloat(m.longitude)}}
        }).filter((m) => m.type !== 'store')
      }
    },
    stores: {
      get() {
        if (!this.posData.length) {
          return []
        }

        return this.posData.map((m, i) => {
          return {...m, idx: i, position: {lat: parseFloat(m.latitude), lng: parseFloat(m.longitude)}}
        }).filter((m) => m.type === 'store')
      }
    },
    center: {
      get() {
        return this.mapCenter;
      }
    }
  },
  methods: {
    handleRightClick: function (event) {
      this.nextProperty = {
        title: 'New Property Name',
        price: '',
        latitude: event.latLng.lat().toString(),
        longitude: event.latLng.lng().toString(),
        imageLink: '',
        link: ''
      }

      this.$emit("add-property", this.nextProperty)

      setTimeout(() => {
        this.centerDialogVisible = true;
      }, 0);
    },
    onUpdateLast: function () {
      this.centerDialogVisible = false;
      this.$emit("update-last-property", this.nextProperty)
    },
    onRemoveLast: function () {
      this.centerDialogVisible = false;
      this.$emit("remove-last-property")
    },
    handleCenterLat: function (value) {
      this.$emit("center-map", {lat: parseFloat(value), lng: this.center.lng})
    },
    handleCenterLng: function (value) {
      this.$emit("center-map", {lat: this.center.lat, lng: parseFloat(value)})
    },
    handleMarkerClick: function (idx) {
      this.$emit("scroll-pos", idx)
    },
    handleIdle: function () {
      const map = this.$refs.mapRef.map;
      const bounds = map.getBounds();

      function getDistance(x1, y1, x2, y2) {
        let y = x2 - x1;
        let x = y2 - y1;

        return Math.sqrt(x * x + y * y);
      }

      let boundLocations = this.locations
          .map((l, i) => {
            const distance = getDistance(l.latitude, l.longitude, this.center.lat, this.center.lng);

            return {...l, distance, posIdx: i};
          })
          .filter((l) => {
            return bounds.contains({lat: parseFloat(l.latitude), lng: parseFloat(l.longitude)})
          })
          .sort((a, b) => a.distance - b.distance)
          .slice(0, 10)

      this.$emit('update-bound-locations', boundLocations);
    },
    handleInfoVisible: function (ref, index) {
      if (this.lastInfoWindow) {
        this.lastInfoWindow.close();
      }

      this.lastInfoWindow = this.$refs[ref][index].infoWindow;
    },
    handleCloseInfoWindow: function () {
      this.lastInfoWindow = null;
    }
  },
  emits: [
    'updateProperty',
    'updateProperties',
    'removeProperties',
    'addProperty',
    'add-property',
    'removeLastProperty',
    'updateLastProperty',
    'center-map',
    'scroll-pos',
    'update-bound-locations',
  ],
});
</script>