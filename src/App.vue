<template>
  <div id="app">
    <components-map :initial-adcode="100000"></components-map>
  </div>
</template>

<script>
import componentsMap from './components/componentsMap.vue';

export default {
  components: {
    componentsMap,
  },
};
</script>
