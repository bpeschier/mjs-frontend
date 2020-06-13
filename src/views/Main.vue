<template>
  <main class="main-view" :class="{'main-view--node': nodeId}">
    <LastMeasurementMap class="main-view__background" :current="current" v-if="!nodeId" />
    <aside class="main-view__node-input">
      <Form class="main-view__node-form">
        <img @click="goHome"
             src="meetjestad.png" class="main-view__logo"
             alt="Meet je stad!" />
        <Field
          autofocus="autofocus"
          label="Node"
          v-model="nodeInput"
          :placeholder="(current) ? getSimpleId(current.node_id) : 'Node number'" />
        <div class="main-view__form-submit" v-if="simpleNodeId !== nodeInput || !nodeId">
          <Button round block type="info" @click="goToNode">
            View node
          </Button>
        </div>
      </Form>
      <ConfigTable :config="config" v-if="nodeId && config" class="main-view__config" />
    </aside>
    <section class="main-view__data" v-if="nodeId && config">
      <Tabs>
        <Tab title="Channel data">
          <ChannelData :config="config" />
        </Tab>
        <Tab title="Configurations"></Tab>
        <Tab title="Message bundles">
          <BundleTable :node-id="nodeId" />
        </Tab>
      </Tabs>
    </section>

  </main>
</template>
<script>

import {
  Button,
  Field,
  Form,
  Tabs,
  Tab,
} from 'vant';
import ConfigTable from '../components/ConfigTable.vue';
import CONFIG_QUERY from '../graphql/config.graphql';
import CONFIG_SUBSCRIPTION from '../graphql/subscription/config.graphql';
import BundleTable from '../components/BundleTable.vue';
import ChannelData from '../components/ChannelData.vue';
import LastMeasurementMap from '../components/LastMeasurementMap.vue';
import BUNDLE_QUERY from '../graphql/bundle.graphql';
import BUNDLE_SUBSCRIPTION from '../graphql/subscription/bundle.graphql';

export default {
  components: {
    LastMeasurementMap,
    ChannelData,
    BundleTable,
    ConfigTable,
    Form,
    Field,
    Button,
    Tabs,
    Tab,
  },
  data() {
    return {
      nodeId: this.getFullId(this.$route.query.node),
      nodeInput: this.getSimpleId(this.$route.query.node),
      config: null,
      bundle: [],
    };
  },
  computed: {
    simpleNodeId() {
      return this.getSimpleId(this.nodeId);
    },
    current() {
      return (this.bundle.length > 0) ? this.bundle[0] : null;
    },
  },
  watch: {
    $route(r) {
      this.nodeId = this.getFullId(r.query.node);
    },
  },
  methods: {
    getSimpleId(nodeId) {
      const matcher = /^ttn\/meet-je-stad\/meetstation-(\d+)$/i;
      const match = matcher.exec(nodeId);
      if (match) {
        return match[1];
      }
      return nodeId;
    },
    getFullId(simpleId) {
      const matcher = /^(\d+)$/i;
      const match = matcher.exec(simpleId);
      if (match) {
        return `ttn/meet-je-stad/meetstation-${simpleId}`;
      }
      return simpleId;
    },
    goToNode() {
      this.$router.push({ to: 'main', query: { node: this.nodeInput } });
    },
    goHome() {
      if (this.nodeId) {
        this.nodeInput = '';
        this.$router.push({ name: 'main' });
      }
    },
  },

  apollo: {
    bundle: {
      query: BUNDLE_QUERY,
      variables() {
        return {
          limit: 1,
        };
      },
      subscribeToMore: {
        document: BUNDLE_SUBSCRIPTION,
        variables() {
          return {
            limit: 1,
          };
        },
        updateQuery(previousResult, { subscriptionData }) {
          this.bundle = subscriptionData.data.bundle;
        },
      },
    },
    config: {
      query: CONFIG_QUERY,
      variables() {
        return {
          limit: 1,
          where: { node_id: { _eq: this.nodeId } },
        };
      },
      skip() {
        return !this.nodeId;
      },
      update(data) {
        if (data.config) {
          return data.config[0];
        }
        return null;
      },
      subscribeToMore: {
        document: CONFIG_SUBSCRIPTION,
        variables() {
          return {
            limit: 1,
            where: { node_id: { _eq: this.nodeId } },
          };
        },
        updateQuery(previousResult, { subscriptionData }) {
          if (subscriptionData.data.config) {
            [this.config] = subscriptionData.data.config;
          }
        },
      },
    },
  },
};
</script>
<style lang="scss">
  .main-view {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    overflow: hidden;

    &--node {
      display: grid;
      grid-template-columns: 320px 1fr;
    }

    &--node &__node-input {
      align-self: flex-start;
    }

    &__node-input {
      max-width: 320px;
      padding: 0.5rem;
    }

    &--node &__node-form {
      margin-top: 0;
    }

    &__node-form {
      margin-top: 150px;
      display: flex;
      flex-direction: column;
    }

    &__node-form &__form-submit {
      margin: 0.5rem;
    }

    &__data {
      align-self: stretch;
      background-color: #fff;
    }

    &__logo {
      position: absolute;
      width: 260px;
      margin-top: -200px;
      opacity: .5;
      align-self: center;
      cursor: pointer;
    }

    &--node &__logo {
      position: static;
      width: 130px;
      margin: 0 0 10px;
    }

    &__background {
      position: absolute;
      top: 0;
      right: 0;
      left: 0;
      bottom: 0;
      opacity: .75;
    }
  }
</style>
