<template>
  <Dropdown
    :options="[
      {
        icon: 'user',
        label: 'My Profile',
        route: {
          name: 'PersonProfile',
          params: { personId: $user().user_profile },
        },
      },
      {
        icon: 'settings',
        label: 'Settings & Members',
        onClick: () => (settingsDialog.show = true),
        condition: () => $user().isNotGuest,
      },
      {
        icon: 'log-out',
        label: 'Log out',
        onClick: () => logout(),
      },
    ]"
  >
    <template v-slot="{ open }">
      <button
        class="flex w-full items-center space-x-2 rounded-md p-2 text-left"
        :class="open ? 'bg-gray-300' : 'hover:bg-gray-200'"
      >
        <UserAvatar :user="$user().name" size="md" />
        <span class="hidden text-base font-medium text-gray-900 sm:inline">
          {{ $user().full_name }}
        </span>
        <FeatherIcon name="chevron-down" class="hidden h-4 w-4 sm:inline" />
      </button>
    </template>
  </Dropdown>
  <SettingsDialog v-model="settingsDialog.show" :tab="settingsDialog.tab" />
</template>
<script>
import { defineAsyncComponent } from 'vue'
import { FeatherIcon, Dropdown, Link } from 'frappe-ui'

export default {
  name: 'UserDropdown',
  components: {
    Dropdown,
    FeatherIcon,
    Link,
    SettingsDialog: defineAsyncComponent(() =>
      import('./Settings/SettingsDialog.vue')
    ),
  },
  data() {
    return {
      settingsDialog: { show: false, tab: null },
    }
  },
  methods: {
    logout() {
      this.$session.logout.submit()
    },
  },
}
</script>
