<script setup lang="ts">
import DataTable from './DataTable.vue'
import { ref } from 'vue'

type Row = { children?: any; data: any; id: string }

defineProps<{
  row: Row
}>()

const subtableExpanded = ref(false)

const handleRowExpand = () => {
  subtableExpanded.value = !subtableExpanded.value
}
</script>

<template>
  <!-- Row is two cols wider than the provided fields! -- Expand and delete buttons -->
  <tr class="[&:nth-child(4n+1)]:bg-neutral-800">
    <td class="p-1 text-center">
      <button
        class="font-bold p-2 hover:font-extrabold text-lg"
        v-on:click="handleRowExpand"
      >
        {{ subtableExpanded ? '▼' : '➤' }}
      </button>
    </td>
    <td class="p-3 text-center" v-for="(field, fieldIndex) in row.data" :key="fieldIndex">
      {{ field }}
    </td>
    <td class="flex items-center justify-center">
      <button
        class="font-bold p-2 text-red-600 hover:font-extrabold text-lg"
        v-on:click="() => {}"
      >
        X
      </button>
    </td>
  </tr>
  <!-- 
    Pseudo-row rendered below the first row.
    It holds the sub-table to not break the first row's table layout.
   -->
  <tr class="[&:nth-child(4n+2)]:bg-neutral-800">
    <!-- Includes delete and expand columns -->
    <td :colspan="Object.keys(row.data).length + 2">
      <div
        v-if="row.children"
        class="flex flex-col p-4 w-full gap-4"
        :class="{ hidden: !subtableExpanded }"
      >
        <div
          v-for="(rowChild, childKey) in row.children"
          :key="childKey"
          :rows="rowChild"
        >
          <DataTable
            v-if="rowChild.records"
            :rows="rowChild.records"
          />
        </div>
      </div>
    </td>
  </tr>
</template>
