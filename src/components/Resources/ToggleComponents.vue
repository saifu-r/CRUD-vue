<template>
  <base-card>
    <base-button @click="setSelectedTab('patient-details')" :mode="showDetails"
      >Patients</base-button
    >
    <base-button @click="setSelectedTab('add-patients')" :mode="addDetails"
      >Add Patients</base-button
    >
  </base-card>
  <keep-alive>
    <component :is="selectedTab"></component>
  </keep-alive>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from "vue";
import AddPatients from "./AddPatients.vue";
import PatientDetails from "./PatientDetails.vue";

export default defineComponent({
  components: { AddPatients, PatientDetails },
  setup() {
    const selectedTab = ref("patient-details");

    const setSelectedTab = (tab: string) => {
      selectedTab.value = tab;
    };

    const showDetails = computed(() => {
      return selectedTab.value === "patient-details" ? null : "flat";
    });
    const addDetails = computed(() => {
      return selectedTab.value === "add-patients" ? null : "flat";
    });

    return { selectedTab, setSelectedTab, showDetails, addDetails };
  },
});
</script>

<style scoped>
</style>
