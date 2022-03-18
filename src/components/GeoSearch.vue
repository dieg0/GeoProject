<template>
  <div>
    <q-input
      outlined
      bottom-slots
      v-model="searchQuery"
      label="UK Postal Code"
      maxlength="12"
      style="padding: 2em"
    >
      <template v-slot:before>
        <q-icon name="place" />
      </template>
      <template v-slot:append>
        <q-icon
          v-if="searchQuery !== ''"
          name="close"
          @click="searchQuery = ''"
          class="cursor-pointer"
        />
        <q-icon
          name="search"
          @click="resolveGeoLocation"
          class="cursor-pointer"
        />
      </template>
    </q-input>
    <leaflet-map :markers="markers"></leaflet-map>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import { api } from "boot/axios";
import { useQuasar } from "quasar";
import LeafletMap from "./LeafletMap.vue";

export default defineComponent({
  name: "GeoSearch",
  components: { LeafletMap },
  setup() {
    const searchQuery = ref("");
    const dense = false;
    const $q = useQuasar();
    const markers = ref([]);

    function resolveGeoLocation() {
      api
        .get("postcodes/" + searchQuery.value)
        .then((response) => {
          const result = response.data.result;
          markers.value = markers.value.concat({
            lat: result.latitude,
            lng: result.longitude,
          });
          $q.notify({
            color: "positive",
            position: "top",
            message: `${searchQuery.value} ZIP Found`,
            icon: "done",
          });
        })
        .catch(() => {
          $q.notify({
            color: "negative",
            position: "top",
            message: `${searchQuery.value} ZIP not found`,
            icon: "report_problem",
          });
        });
    }

    return { searchQuery, dense, resolveGeoLocation, markers };
  },
  methods: {},
});
</script>
