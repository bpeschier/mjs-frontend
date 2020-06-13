<template>
  <div class="last-measurement-map" v-if="currentPoint">
    <l-map
      :options="{zoomControl: false, attributionControl: false}"
      class="map__map"
      :center="currentPoint"
      :zoom="10"
    >
      <l-control-attribution position="bottomleft" />
      <l-tile-layer :url="url" :detectRetina="true" :attribution="attribution" />
      <l-marker :lat-lng="currentPoint" />
    </l-map>
  </div>
</template>

<script>
import { Icon } from 'leaflet';

import {
  LControlAttribution,
  LMap,
  LMarker,
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
    LControlAttribution,
    LTileLayer,
  },
  props: {
    current: {
      type: Object,
      default: () => null,
    },
  },
  data() {
    return {
      bundle: [],
      url: '//stamen-tiles-{s}.a.ssl.fastly.net/terrain/{z}/{x}/{y}.png',
      // url: 'https://{s}.tile.osm.org/{z}/{x}/{y}.png',
      attribution: 'Map tiles by <a href="http://stamen.com">Stamen Design</a>, under <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a>. Data by <a href="http://openstreetmap.org">OpenStreetMap</a>, under <a href="http://www.openstreetmap.org/copyright">ODbL</a>.',
    };
  },
  computed: {
    currentPoint() {
      const p = this.current?.data.position;
      if (p) {
        return { lat: p.value[0], lon: p.value[1] };
      }
      return null;
    },
  },

};
</script>
<style lang="scss">

  .last-measurement-map {
  }
</style>
