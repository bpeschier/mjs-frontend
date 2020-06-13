<template>
  <div class="channel-plotter">
    <slot :data="data" :unit="channel.unit" />
  </div>
</template>
<script>
import {
  addHours,
  formatISO,
  parseISO,
  formatISO9075,
} from 'date-fns';
import MEASUREMENT_QUERY from '../graphql/measurement.graphql';
import MEASUREMENT_SUBSCRIPTION from '../graphql/subscription/measurement.graphql';

export default {
  props: {
    config: {
      type: Object,
      default: () => null,
    },
    channel: {
      type: Object,
      default: () => null,
    },
  },

  data() {
    return {
      measurements: [],
    };
  },

  computed: {
    data() {
      return this.measurements?.map((measurement) => ({
        timestamp: formatISO9075(parseISO(measurement.timestamp)),
        [this.channel.unit]: measurement.data.value,
      }));
    },
    timestampCutoff() {
      return formatISO(addHours(new Date(), -24), { representation: 'complete' });
    },
  },

  apollo: {
    measurements: {
      query: MEASUREMENT_QUERY,
      variables() {
        return {
          where: {
            channel_id: { _eq: this.channel.channel_id },
            node_id: { _eq: this.config.node_id },
            timestamp: { _gt: this.timestampCutoff },
          },
        };
      },
      update(data) {
        return data.measurement;
      },
      subscribeToMore: {
        document: MEASUREMENT_SUBSCRIPTION,
        variables() {
          return {
            where: {
              channel_id: { _eq: this.channel.channel_id },
              node_id: { _eq: this.config.node_id },
              timestamp: { _gt: this.timestampCutoff },
            },
          };
        },
        updateQuery(previousResult, { subscriptionData }) {
          this.measurement = subscriptionData.data.measurement;
        },
      },
    },
  },

};
</script>
