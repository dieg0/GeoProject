<template>
  <q-page>
    <geo-search></geo-search>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import GeoSearch from "src/components/GeoSearch.vue";

export default defineComponent({
  name: "IndexPage",
  components: { GeoSearch },
});
</script>
