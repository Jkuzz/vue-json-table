<script setup lang="ts">
import DataTableRow from './DataTableRow.vue'

defineProps<{
  rows: { children?: any; data: any; id: string }[]
}>()
</script>

<template>
  <table class="table-auto" v-if="rows.length > 0">
    <thead class="bg-green-400 text-neutral-900 font-bold">
      <tr>
        <td>
          <!-- column for the expand button -->
        </td>
        <td class="p-3 text-center" v-for="key in Object.keys(rows[0].data)" :key="key">
          {{ key }}
        </td>
        <td class="p-3 text-center">delete</td>
      </tr>
    </thead>
    <tbody>
      <DataTableRow
        v-for="row in rows"
        :key="row.id"
        :row="row"
      />
    </tbody>
  </table>
</template>
