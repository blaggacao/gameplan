<template>
  <div class="py-6">
    <div class="mb-4.5 flex items-center justify-between">
      <h2 class="text-xl font-semibold text-gray-900">My Tasks</h2>
      <div class="flex items-stretch space-x-2">
        <Select
          :options="[
            { label: 'All', value: 'all' },
            { label: 'Active', value: 'active' },
            { label: 'Backlog', value: 'backlog' },
            { label: 'Done', value: 'done' },
          ]"
          v-model="listType"
        />
        <Button variant="solid" @click="showNewTaskDialog">
          <template #prefix>
            <FeatherIcon class="h-4 w-4" name="plus" />
          </template>
          Add new
        </Button>
      </div>
    </div>
    <TaskList :listOptions="{ filters }" />
    <NewTaskDialog ref="newTaskDialog" />
  </div>
</template>
<script setup>
import { ref } from 'vue'
import { Select, getCachedListResource } from 'frappe-ui'
import { getUser } from '@/data/users'
import { computed } from 'vue'

let listType = ref('active')
let newTaskDialog = ref(null)

const filters = computed(() => {
  let sessionUser = getUser('sessionUser').name
  let filters = {
    assigned_to: sessionUser,
    owner: sessionUser,
  }
  if (listType.value === 'active') {
    filters.status = ['in', ['Todo', 'In Progress']]
  } else if (listType.value === 'backlog') {
    filters.status = 'Backlog'
  } else if (listType.value === 'done') {
    filters.status = 'Done'
  }
  return filters
})

function showNewTaskDialog() {
  let status = 'Backlog'
  if (listType.value === 'active') {
    status = 'Todo'
  } else if (listType.value === 'done') {
    status = 'Done'
  }
  newTaskDialog.value.show({
    defaults: {
      status,
      assigned_to: getUser('sessionUser').name,
    },
    onSuccess: () => {
      let listOptions = { filters: filters.value }
      let tasks = getCachedListResource(['Tasks', listOptions])
      tasks.reload()
    },
  })
}
</script>
