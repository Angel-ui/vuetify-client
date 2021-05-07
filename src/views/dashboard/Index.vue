<template>
  <v-app>
    <dashboard-core-app-bar />

    <dashboard-core-drawer />

    <dashboard-core-view />
  </v-app>
</template>

<script>
  export default {
    name: 'DashboardIndex',

    components: {
      DashboardCoreAppBar: () => import('./components/core/AppBar'),
      DashboardCoreDrawer: () => import('./components/core/Drawer'),
      DashboardCoreView: () => import('./components/core/View'),
    },

    data: () => ({
      expandOnHover: false,
    }),
    mounted () {
      // eslint-disable-next-line no-unused-expressions
      localStorage.getItem('AccessToken') == null
        ? this.$router.push({ name: 'Login' }).catch(() => {}) : ''
      this.validateSession()
    },
    methods: {
      validateSession () {
        const session = JSON.parse(localStorage.getItem('AccessToken'))
        // eslint-disable-next-line no-unused-expressions
        new Date().getTime() > session.expiry ? localStorage.removeItem('AccessToken') : ''
        // eslint-disable-next-line no-unused-expressions
        localStorage.getItem('AccessToken') == null ? this.$router.push({ name: 'Login' }).catch(() => {}) : ''
      },
    },
  }
</script>
