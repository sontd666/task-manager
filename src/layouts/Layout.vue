<template>
  <q-layout view="hHh lpr fFf">
    <q-header elevated class="bg-indigo">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          icon="menu"
          aria-label="Menu"
        />

        <q-toolbar-title class="absolute-center">
          Task Manager
        </q-toolbar-title>

        <q-btn
          v-if="!loggedIn"
          to="/auth"
          flat
          icon-right="account_circle"
          label="Login"
          class="absolute-right" />

        <q-btn
          v-else
          @click="logoutUser"
          flat
          icon-right="logout"
          label="Logout"
          class="absolute-right" />

      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      :breakpoint="767"
      :width="220"
      show-if-above
      bordered
      content-class="bg-indigo-6"
    >
      <q-list dark>
        <q-item-label header>Navigation</q-item-label>
        <q-item
          v-for="nav in navs"
          :key="nav.label"
          :to="nav.to"
          exact
          clickable>
          <q-item-section avatar>
            <q-icon :name="nav.icon" />
          </q-item-section>
          <q-item-section>
            <q-item-label>{{ nav.label }}</q-item-label>
          </q-item-section>
        </q-item>

      <!-- Quit submenu - Only for Electron -->
        <q-item
          v-if="$q.platform.is.electron"
          @click="quitApp"
          class="absolute-bottom"
          clickable>
          <q-item-section avatar>
            <q-icon name="power_settings_new" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Quit</q-item-label>
          </q-item-section>
        </q-item>

      </q-list>
    </q-drawer>

    <q-footer>
        <q-tabs>
        <q-route-tab
          v-for="nav in navs"
          :key="nav.label"
          :to="nav.to"
          exact
          :icon="nav.icon"
          :label="nav.label" />
        </q-tabs>
      </q-footer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { mapState, mapActions } from 'vuex'

export default {
  name: 'MyLayout',

  data () {
    return {
      leftDrawerOpen: false,
      navs: [
        {
          label: 'Tasks',
          icon: 'list',
          to: '/'
        },
        // {
        //   label: 'Settings',
        //   icon: 'settings',
        //   to: '/settings'
        // },
        {
          label: 'Admin',
          icon: 'supervisor_account',
          to: '/admin'
        },
      ]
    }
  },
  methods: {
    ...mapActions('auth', ['logoutUser']),

    quitApp() {
      this.$q.dialog({
        title: 'Confirm',
        message: 'Sure you want to quit?',
        cancel: true,
        persistent: true,
      }).onOk(() => {
        if (this.$q.platform.is.electron) {
          require('electron').ipcRenderer.send('quit-app')
        }
      })
    }
  },
  computed: {
    ...mapState('auth', ['loggedIn'])
  }
}
</script>

<style lang="stylus">
  .q-drawer
    .q-router-link--exact-active
      color: $amber-cust

  .q-platform-ios
    .q-header
      padding-top constant(safe-area-inset-top)
      padding-top env(safe-area-inset-top)

    .q-footer
      padding-bottom constant(safe-area-inset-bottom)
      padding-bottom env(safe-area-inset-bottom)

  @media screen and (min-width: 768px)
    .q-footer
      display: none

</style>
