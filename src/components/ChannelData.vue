<template>
  <div class="channel-data">
    <section
      v-for="channel in channels"
      :key="channel.channel_id"
      class="channel-data__section">
      <ChannelPlotter :channel="channel" :config="config">
        <template #default="props">
          <component :is="componentForChannel(channel)" v-bind="props" />
        </template>
      </ChannelPlotter>
    </section>
  </div>
</template>
<script>
import DefaultLineGraph from './DefaultLineGraph.vue';
import ChannelPlotter from './ChannelPlotter.vue';
import DefaultMap from './DefaultMap.vue';

export default {
  components: { ChannelPlotter },
  props: {
    config: {
      type: Object,
      default: () => null,
    },
  },

  computed: {
    channels() {
      return Object.entries(this.config.data.channel_config).map(([k, c]) => ({
        ...c,
        channel_id: Number.parseInt(k, 10),
      }));
    },
  },

  methods: {
    componentForChannel(channel) {
      switch (channel.quantity) {
        case 'position':
          return DefaultMap;
        default:
          return DefaultLineGraph;
      }
    },
  },
};
</script>
<style lang="scss">
  .channel-data {
    overflow: auto;

    &__section {
      padding: 1rem;
    }
  }
</style>
