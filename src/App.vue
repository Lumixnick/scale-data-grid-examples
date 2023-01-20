<script setup>
import { ScaleDataGrid } from '@telekom/scale-components/dist/components/scale-data-grid';
import { ScaleAppShell } from '@telekom/scale-components/dist/components/scale-app-shell';
import ServiceTable from '/src/components/ServiceTable.vue';

if (!customElements.get('custom-scale-data-grid')) {
  customElements.define('custom-scale-data-grid', ScaleDataGrid);
}
if (!customElements.get('custom-scale-app-shell')) {
  customElements.define('custom-scale-app-shell', ScaleAppShell);
}
</script>

<template>
  <div>
    <scale-tab-nav size="large">
      <scale-tab-header slot="tab">First</scale-tab-header>
      <scale-tab-panel slot="panel">
        <ServiceTable />
      </scale-tab-panel>
      <scale-tab-header slot="tab"> Second</scale-tab-header>
      <scale-tab-panel slot="panel"></scale-tab-panel>
    </scale-tab-nav>
  </div>
</template>
