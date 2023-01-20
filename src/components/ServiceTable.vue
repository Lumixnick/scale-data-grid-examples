<template>
  <div>
    <div><h4>MS hashtable</h4></div>
    <div style="padding: 5px 0">
      <scale-button style="padding: 0 5px" @click="openDialog()"
        >add service(s)</scale-button
      >
      <scale-button @click="clearTableAndRefValues()">clear table</scale-button>
      <scale-modal heading="Today is your lucky day" id="modalService">
        <scale-textarea rows="3" id="serviceInputValues"></scale-textarea>
        <scale-button slot="action" variant="Save" @click="closeModal()">
          Cancel
        </scale-button>
        <scale-button slot="action" @click="handleDataGridValuesFromDialog">
          Save
        </scale-button>
      </scale-modal>
    </div>
    <div>
      <custom-scale-data-grid
        id="serviceDataGrid"
        :rows="tableRowsRef"
        :fields="fields"
        hide-menu
        sortable
      />
    </div>
  </div>
</template>

<script lang="ts">
import { ScaleDataGrid } from '@telekom/scale-components/dist/components/scale-data-grid';
import { ScaleModal } from '@telekom/scale-components/dist/components/scale-modal';
import { ScaleTextarea } from '@telekom/scale-components/dist/components/scale-textarea';
import { defineComponent, ref, onMounted, nextTick } from 'vue';
import { setEnvironmentData } from 'worker_threads';
import serviceElement from '../types/serviceElement';

if (!customElements.get('custom-scale-data-grid')) {
  customElements.define('custom-scale-data-grid', ScaleDataGrid);
}

export default defineComponent({
  name: 'ServiceTable',

  setup(props) {
    const fields = ref([
      {
        type: 'text',
        label: 'MS',
        style: 'checkbox',
        sortable: true,
      },
      {
        type: 'text',
        label: 'source-githash',
        style: 'switch',
        sortable: true,
      },
      {
        type: 'text',
        label: 'newest-githash',
        style: 'switch',
        sortable: true,
      },
      {
        type: 'text',
        label: 'flyway-scxript-changed',
        style: 'checkbox',
        sortable: true,
      },
    ]);
    const tableRowsRef = ref('');

    function openDialog() {
      const modal = document.getElementById('modalService') as ScaleModal;
      modal.opened = true;
      const textAreaForModalInput = document.getElementById(
        'serviceInputValues'
      ) as ScaleTextarea;
      textAreaForModalInput.value =
        'first,4f043c7d,4f043c7d,false\nsecond,7be69785,7be69785,true\nthird,8a5a110f,8a5a110f,false';
    }
    function closeModal() {
      const modal = document.getElementById('modalService') as ScaleModal;
      modal.opened = false;
    }

    //Clear table and reflective values
    function clearTableAndRefValues() {
      tableRowsRef.value = '';

      resetLocalStorage();
    }

    function resetLocalStorage() {
      localStorage.removeItem('gitHashesTable');
    }

    function storeData() {
      //Store it
      localStorage.setItem(
        'gitHashesTable',
        JSON.stringify(tableRowsRef.value as string)
      );
    }

    function handleDataGridValuesFromDialog() {
      //First get the value from the dialog
      //Get the value from the modal dialog
      let textAreaForModalInput = document.getElementById(
        'serviceInputValues'
      ) as ScaleTextarea;
      //close Dialog
      closeModal();
      //cleanTable
      clearTableAndRefValues();
      resetLocalStorage();
      //Array is needed for the scale-data-grid
      let inputArrayForDataGrid = new Array();
      let valueString: string = textAreaForModalInput.value as string;
      //spit in lines
      inputArrayForDataGrid = valueString.split('\n');
      //rows for DataGrid
      tableRowsRef.value = createScaleDataGridStringForRows(
        inputArrayForDataGrid
      );

      storeData();
    }

    function createDataGridStringFromDialog(inputArrayForDataGrid: Array<any>) {
      flywayServicesForEmail.value = createFlywayServicesForEmail(
        inputArrayForDataGrid
      );
      console.log(
        'ServiceTable:createFlywayServicesForEmail:flywayServicesForEmail:',
        JSON.stringify(flywayServicesForEmail.value)
      );
    }

    function createScaleDataGridStringForRows(
      inputArrayForDataGrid: Array<any>
    ) {
      //Input values must be in format a,b,c,true/false
      let scaleDataGridStringForRows: string = '[';

      for (let i = 0; i < inputArrayForDataGrid.length; i++) {
        let wert = new Array(inputArrayForDataGrid[i].split(','));
        for (let ar = 0; ar < wert.length; ar++) {
          scaleDataGridStringForRows += JSON.stringify(wert[ar]);
          scaleDataGridStringForRows += ',';
        }
      }
      //delete last comma
      scaleDataGridStringForRows = scaleDataGridStringForRows.substring(
        0,
        scaleDataGridStringForRows.length - 1
      );
      scaleDataGridStringForRows += ']';
      console.log(
        'ServiceTable:createScaleDataGridStringForRows:scaleDataGridStringForRows:',
        scaleDataGridStringForRows
      );
      return scaleDataGridStringForRows;
    }

    onMounted(() => {
      if (localStorage.getItem('gitHashesTable') != null) {
        let value = JSON.parse(
          localStorage.getItem('gitHashesTable') as string
        );
        console.log('ServiceTable:onMounted:', value);
        tableRowsRef.value = value;
      }
    });
    return {
      handleDataGridValuesFromDialog,
      createDataGridStringFromDialog,
      closeModal,
      openDialog,
      clearTableAndRefValues,
      resetLocalStorage,
      fields,
      tableRowsRef,
    };
  },
});
</script>

<style></style>
