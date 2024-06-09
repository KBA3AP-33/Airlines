<template>
  <main class="page-container">
    <Filter
      :class="['page-filter', (isFilterVisible) && 'open']"
      :items="startItems"
      @openCloseFilter="openCloseFilter"
      @changeFilter="items = $event"/>
    <Catalog :catalog-items="items" @openCloseFilter="openCloseFilter"/>
  </main>
</template>

<script lang="ts">
  import result from "@/data/flights.json";
  import Filter from "@/components/Filter.vue";
  import Catalog from "@/components/Catalog.vue";
  import type { Flight, Result } from "./types";


  export default {
    data: () => ({
      isFilterVisible: false,
      startItems: (result as Result).result.flights.map(e => e.flight),
      items: [] as Array<Flight>,
    }),
    methods: {
      openCloseFilter(isVisible?: boolean) {
        this.isFilterVisible = (typeof(isVisible) !== 'undefined') ? isVisible : !this.isFilterVisible;
      }
    },
    components: { Filter, Catalog },
  }
</script>

<style scoped>
  .page-container {
    display: flex;
    justify-content: center;
    gap: 10px;
    padding: 20px 0;
    position: relative;

    .page-filter {
      width: 320px;
      min-height: calc(100vh - 40px);
      flex-shrink: 0;
    }
  }
</style>
