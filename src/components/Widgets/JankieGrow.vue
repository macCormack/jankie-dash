<template>
<div class="grow-wrapper">
  <div class="column">
    <div class="categories">Temp</div>
    <div>{{ this.results.temp }} F</div>
  </div>
  <div class="column">
    <div class="categories">Humidity</div>
    <div>{{ results.hum }}</div>
  </div>
  <div class="column">
    <div class="categories">Light</div>
    <div>{{ results.light}}</div>

  </div>
</div>
</template>

<script>

import WidgetMixin from '@/mixins/WidgetMixin';
import { widgetApiEndpoints } from '@/utils/defaults';

export default {
  mixins: [WidgetMixin],
  data() {
    return {
      results: [],
    };
  },
  computed: {
    tokenUuid() {
      if (!this.options.token_uuid) this.error('token uuid is required');
      return this.options.token_uuid || '';
    },
    endpoint() {
      return `${widgetApiEndpoints.jankieGrow}/recent-data`;
    },
    authHeaders() {
      if (this.tokenUuid) {
        return { 'x-api-key': `${this.tokenUuid } `};
      }
      return false;
    },
  },
  methods: {
    fetchData() {
      const auth = this.authHeaders;
      if (auth) {
        this.startLoading();
        this.makeRequest(this.endpoint, auth).then(this.processData);
      }
    },
    processData(data) {
      // Do processing any here, and set component data
      // this.results = data;
      this.results.temp = data.temperatureF.toFixed(0);
      this.results.hum = data.humidityRh.toFixed(0);
      this.results.light = data.lightLux.toFixed(0);
      this.finishLoading();
    },
  },
};
</script>

<style scoped lang="scss">
.grow-wrapper {
display:flex;
flex-wrap: wrap;
justify-content: center;
}
.column {
  width:22%;
  padding:10px;
}
.column > div {
  text-align: center;
}
.categories {
  font-weight: bold;
  padding-bottom:6px;
}
</style>
