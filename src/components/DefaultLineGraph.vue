<template>
  <div>
    <D3LineChart :datum="data" :config="chart_config" />
  </div>
</template>
<script>

import { D3LineChart } from 'vue-d3-charts';

export default {
  components: { D3LineChart },
  props: {
    data: {
      type: Array,
      default: () => [],
    },
    unit: {
      type: String,
      default: () => '',
    },
  },
  data() {
    return {
      chart_config: {
        values: [this.unit],
        date: {
          key: 'timestamp',
          inputFormat: '%Y-%m-%d %H:%M:%S',
          outputFormat: '%H:%M',
        },
        curve: 'curveCatmullRom',
        axis: {
          yTitle: this.unit,
          yTicks: 3,
          xTicks: 12,
        },
        tooltip: {
          labels: true,
        },
        transition: {
          duration: 10,
          ease: 'easeLinear',
        },
      },
    };
  },
};
</script>
