<template>
  <div class="flex min-w-0 items-center">
    <template v-if="dropdownItems.length">
      <Dropdown class="h-7" :options="dropdownItems">
        <Button variant="ghost">
          <template #icon>
            <LucideMoreHorizontal class="w-4 text-gray-600" />
          </template>
        </Button>
      </Dropdown>
      <span class="ml-1 mr-0.5 text-base text-gray-500"> / </span>
    </template>
    <div
      class="flex min-w-0 items-center overflow-hidden text-ellipsis whitespace-nowrap"
    >
      <template v-for="(item, i) in linkItems" :key="item.label">
        <router-link
          class="flex items-center rounded px-0.5 py-1 text-lg font-medium focus:outline-none focus-visible:ring-2 focus-visible:ring-gray-400"
          :class="[
            i == linkItems.length - 1
              ? 'text-gray-900'
              : 'text-gray-600 hover:text-gray-700',
          ]"
          :to="item.route"
        >
          <slot name="prefix" :item="item" />
          <span>
            {{ item.label }}
          </span>
        </router-link>
        <span
          v-if="i != linkItems.length - 1"
          class="mx-0.5 text-base text-gray-500"
        >
          /
        </span>
      </template>
    </div>
  </div>
</template>
<script setup>
import { useWindowSize } from '@vueuse/core'
import { computed } from 'vue'
import { useRouter } from 'vue-router'
import { Dropdown } from 'frappe-ui'

const props = defineProps({
  items: {
    type: Array,
    required: true,
  },
})

const router = useRouter()
const { width } = useWindowSize()

const dropdownItems = computed(() => {
  if (width.value > 640) return []

  let allExceptLastTwo = props.items.slice(0, -2)
  return allExceptLastTwo.map((item) => ({
    ...item,
    icon: null,
    label: item.label,
    onClick: () => router.push(item.route),
  }))
})

const linkItems = computed(() => {
  if (width.value > 640) return props.items

  let lastTwo = props.items.slice(-2)
  return lastTwo
})
</script>
