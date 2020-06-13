<template>
  <div class="map" v-if="current">
    <l-map
      :options="{attributionControl: false}"
      class="map__map"
      :center="current"
      :zoom="18"
    >
      <l-control-attribution position="bottomleft" />
      <l-tile-layer :url="url" :detectRetina="true" attribution="© OpenStreetMap contributors" />
      <l-polyline :lat-lngs="path" color="red"></l-polyline>
      <l-marker :lat-lng="current" />
    </l-map>
  </div>
</template>

<script>
import { Icon } from 'leaflet';

import {
  LMap,
  LMarker,
  LPolyline,
  LControlAttribution,
  LTileLayer,
} from 'vue2-leaflet';
import 'leaflet/dist/leaflet.css';

// E_NO_CLUE: https://vue2-leaflet.netlify.app/quickstart/#marker-icons-are-missing
// eslint-disable-next-line
delete Icon.Default.prototype._getIconUrl;
Icon.Default.mergeOptions({
  // eslint-disable-next-line
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  // eslint-disable-next-line
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  // eslint-disable-next-line
  shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
});

export default {
  inheritAttrs: false,
  components: {
    LMap,
    LMarker,
    LPolyline,
    LControlAttribution,
    LTileLayer,
  },
  props: {
    data: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      url: 'https://{s}.tile.osm.org/{z}/{x}/{y}.png',
    };
  },
  computed: {
    path() {
      return this.data.map((point) => ({
        lat: point.degrees[0],
        lon: point.degrees[1],
      }));
    },
    current() {
      if (this.data.length > 0) {
        const current = this.data[this.data.length - 1].degrees;
        return { lat: current[0], lon: current[1] };
      }
      return null;
    },
  },
};
</script>
<style lang="scss">

  .map {
    height: 300px;


  }
</style>
