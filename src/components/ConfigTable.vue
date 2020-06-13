<template>
  <div v-if="config">
    <CellGroup title="Configuration">
      <Cell title="ID" :label="config.node_id" size="large" />
      <Cell title="Used since" :label="timestamp" size="large" />
      <Cell :title="key" :key="key"
            v-for="[key, v] of Object.entries(config.data.node_config)">
        {{ v }}
      </Cell>
    </CellGroup>
    <CellGroup title="Channels" v-if="channels">
      <Cell :title="channelTitle(key, channel)" :key="key"
            v-for="[key, channel] of Object.entries(channels)">
        {{ channelUnit(channel) }}
      </Cell>
    </CellGroup>
  </div>
</template>
<script>

import { parseISO } from 'date-fns';

import {
  Cell,
  CellGroup,
} from 'vant';


export default {
  components: { CellGroup, Cell },
  props: {
    config: {
      type: Object,
      default: () => null,
    },
  },

  computed: {
    channels() {
      return this.config.data.channel_config;
    },
    timestamp() {
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
        options).format(parseISO(`${this.config.timestamp}Z`));
    },
  },

  methods: {
    channelTitle(key, channel) {
      if (channel.measured) {
        return `{${key}} ${channel.measured}:${channel.quantity}`;
      }
      return `{${key}} ${channel.quantity}`;
    },
    channelUnit(channel) {
      if (channel.divider) {
        return `${channel.unit} (/${channel.divider})`;
      }
      return channel.unit;
    },
  },

};
</script>
