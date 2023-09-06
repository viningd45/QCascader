<script>
import Vue from 'vue';
import { QCascaderSample } from '@/entry';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    QCascaderSample,
  }
});
</script>

<template>
  <div id="app">
    <q-cascader-sample />
  </div>
</template>
