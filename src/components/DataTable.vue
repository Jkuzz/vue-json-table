<script setup lang="ts">
import {ref, computed} from 'vue'
import DataTableRow from './DataTableRow.vue'

const props = defineProps<{
  rows: { children?: any; data: any; id: string }[]
}>()

const emit = defineEmits<{
  /**
   * All of this table's rows were deleted.
   */
  (e: 'emptied'): void
}>()

/**
 * Filtered data rows. Filtered as computed to not mutate props.
 */
const filteredRows = computed(() =>
  props.rows.filter((row) => !rowsFilterIds.value.includes(row.id))
)
const rowsFilterIds = ref<string[]>([])

const handleDeleteRow = (rowId: string) => {
  rowsFilterIds.value.push(rowId)
  if (filteredRows.value.length <= 0) {
    emit('emptied')
  }
}
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
        v-for="row in filteredRows"
        :key="row.id"
        :row="row"
        @delete="handleDeleteRow"
      />
    </tbody>
  </table>
</template>
