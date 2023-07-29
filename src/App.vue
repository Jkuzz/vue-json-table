<script setup lang="ts">
import { ref } from 'vue'
import { v4 as uuidv4 } from 'uuid'
import DataTable from '@/components/DataTable.vue'
import '@/assets/main.css'

// TODO: better typing
const data = ref<any[]>([])

//TODO: better fetching than fetch? @tanstack/vue-query
//TODO: validation?
fetch('example-data.json')
  .then((res) => res.json())
  .then((res: any[]) => {
    data.value = createItemUids(res)
  })

  /**
   * Add unique ids to data rows. Needed because the received data can have non-unique ids.
   * @param data data with missing uids
   */
const createItemUids = (data: any[]) => {
  data.forEach((item: any) => {
    // Attatch unique id to items
    if (!item.id) {
      item.id = uuidv4()
    }

    if (!item.children) return
    // Iterate over all existing child groups
    for (let childGroup in item.children) {
      // Call recursively on item's children
      for (let childItem in item.children[childGroup]) {
        createItemUids(item.children[childGroup][childItem])
      }
    }
  })
  return data
}
</script>

<template>
  <main>
    <Suspense>
      <DataTable :rows="data" />
      <template #fallback> Loading... </template>
    </Suspense>
  </main>
</template>
