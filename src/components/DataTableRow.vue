<script setup lang="ts">
import DataTable from './DataTable.vue'
import { ref, computed } from 'vue'

type Row = { children?: any; data: any; id: string }

const props = defineProps<{
  row: Row
}>()

const emit = defineEmits<{
  /**
   * This row was marked to be deleted.
   */
  (e: 'delete', id: string): void
}>()

const subtableExpanded = ref(false)

/**
 * Initialised with prop subtable keys.
 * Subtable is marked as true if it is empty.
 */
const emptySubtables = ref(
  Object.fromEntries(
    Object.keys(props.row.children).map((key) => [
      key,
      Object.keys(props.row.children[key]).length === 0
    ])
  )
)

/**
 * Cannot be computed, as the rows are filtered internally within the child table component.
 */
const allSubtablesEmpty = computed(
  () =>
    Object.keys(emptySubtables.value).length === 0 ||
    !Object.values(emptySubtables.value).includes(false)
)

const handleRowExpand = () => {
  subtableExpanded.value = !subtableExpanded.value
}

const handleDelete = (rowToDelete: Row) => {
  emit('delete', rowToDelete.id)
}

const handleSubtableEmptied = (subtable: string) => {
  emptySubtables.value[subtable] = true
}
</script>

<template>
  <!-- Row is two cols wider than the provided fields! -- Expand and delete buttons -->
  <tr class="[&:nth-child(4n+1)]:bg-neutral-800">
    <td class="p-1 text-center">
      <button
        class="font-bold p-2 hover:font-extrabold text-lg"
        v-if="!allSubtablesEmpty"
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
        v-on:click="() => handleDelete(row)"
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
        :class="{ hidden: !subtableExpanded || allSubtablesEmpty }"
      >
        <div
          v-for="(rowChild, childKey) in row.children"
          :key="childKey"
          :rows="rowChild"
          :class="{ hidden: emptySubtables[childKey] }"
        >
          <DataTable
            @emptied="() => handleSubtableEmptied(childKey.toString())"
            v-if="rowChild.records"
            :rows="rowChild.records"
          />
        </div>
      </div>
    </td>
  </tr>
</template>
