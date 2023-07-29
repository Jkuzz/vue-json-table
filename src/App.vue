<script setup lang="ts">
import { ref } from 'vue'
import DataTable from '@/components/DataTable.vue'
import '@/assets/main.css'

// TODO: better typing
const data = ref<any[]>([])

//TODO: better fetching than fetch? @tanstack/vue-query
//TODO: validation?
fetch('example-data.json')
  .then((res) => res.json())
  .then((res: any[]) => {
    data.value = res
  })

</script>

<template>
  <main>
    <Suspense>
      <DataTable :rows="data" />
      <template #fallback> Loading... </template>
    </Suspense>
  </main>
</template>
