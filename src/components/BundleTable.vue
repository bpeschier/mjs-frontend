<template>
  <table class="bundle-table">
    <thead>
    <tr>
      <th class="bundle-table__timestamp">Timestamp</th>
      <th class="bundle-table__data">Data</th>
    </tr>
    </thead>
    <transition-group name="list" tag="tbody">
      <tr v-for="m in bundle" :key="m.message_id" class="bundle-table__line">
        <td class="bundle-table__timestamp">
          {{ timestamp(m.timestamp) }}
        </td>
        <td>
          <pre>{{ m.data }}</pre>
        </td>
      </tr>
    </transition-group>
  </table>
</template>

<script>

import { parseISO } from 'date-fns';
import BUNDLE_QUERY from '../graphql/bundle.graphql';
import BUNDLE_SUBSCRIPTION from '../graphql/subscription/bundle.graphql';

export default {
  props: {
    nodeId: {
      type: String,
      default: () => null,
    },
  },
  data() {
    return {
      limit: 10,
      bundle: [],
    };
  },

  methods: {
    timestamp(timestamp) {
      const options = {
        year: 'numeric',
        month: 'numeric',
        day: 'numeric',
        hour: 'numeric',
        minute: 'numeric',
        second: 'numeric',
        timeZone: 'Europe/Amsterdam',
        timeZoneName: 'short',
      };
      return Intl.DateTimeFormat('nl-NL',
        options).format(parseISO(`${timestamp}Z`));
    },
  },

  apollo: {
    bundle: {
      query: BUNDLE_QUERY,
      variables() {
        return {
          limit: this.limit,
          where: { node_id: { _eq: this.nodeId } },
        };
      },
      subscribeToMore: {
        document: BUNDLE_SUBSCRIPTION,
        variables() {
          return {
            limit: this.limit,
            where: { node_id: { _eq: this.nodeId } },
          };
        },
        updateQuery(previousResult, { subscriptionData }) {
          this.bundle = subscriptionData.data.bundle;
        },
      },
    },
  },


};
</script>
<style lang="scss">
  .bundle-table {
    width: 100%;

    &__timestamp {
      width: 100px;
    }

    pre {
      margin: 0;
      padding: 10px;
      background-color: #eee;
    }

    th, td {
      text-align: left;
      vertical-align: top;
      padding: 10px;
    }

  }

  .list-move {
    transition: transform 300ms;
  }
</style>
