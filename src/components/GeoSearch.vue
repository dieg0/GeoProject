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
          @click="receiveUserPostCode"
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
    const existingPostCodes = [
      "EC2A 3AG",
      "N1 1RA",
      "W12 9BL",
      "W1U 6AA",
      "SW7 3HZ",
      "SW7 3DX",
      "W11 4UA",
      "SW13 9LB",
      "HP9 2JH",
      "HP9 1QD",
      "N1 2UQ",
      "W2 2HU",
      "W4 5DG",
      "NW5 2HR",
      "W11 2SE",
      "W8 6QD",
      "W11 3HL",
      "OL2 8NP",
      "W11 1LA",
      "NW3 2PT",
    ];
    const dense = false;
    const $q = useQuasar();
    const markers = ref([]);

    function receiveUserPostCode() {
      markers.value = [];
      resolveGeoLocation(searchQuery.value, "user");
      existingPostCodes.forEach((postCode) => {
        resolveGeoLocation(postCode, "existing");
      });
    }

    function resolveGeoLocation(zipCode, markerType) {
      api
        .get("postcodes/" + zipCode)
        .then((response) => {
          const result = response.data.result;
          markers.value.push({
            lat: result.latitude,
            lng: result.longitude,
            markerType: markerType,
          });
          if (markerType == "user") {
            $q.notify({
              color: "positive",
              position: "bottom-right",
              message: `${zipCode} ZIP Found`,
              icon: "done",
            });
          }
        })
        .catch(() => {
          $q.notify({
            color: "negative",
            position: "bottom-right",
            message: `${zipCode} ZIP not found`,
            icon: "report_problem",
          });
        });
    }

    return { searchQuery, dense, receiveUserPostCode, markers };
  },
  methods: {},
});
</script>
