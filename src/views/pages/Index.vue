<template>
  <v-app>
    <pages-core-app-bar />

    <pages-core-view />

    <pages-core-footer />
  </v-app>
</template>

<script>
  export default {
    name: 'PagesIndex',

    components: {
      PagesCoreAppBar: () => import('./components/core/AppBar'),
      PagesCoreFooter: () => import('./components/core/Footer'),
      PagesCoreView: () => import('./components/core/View'),
    },

    created () {
      // eslint-disable-next-line no-unused-expressions
      localStorage.getItem('AccessToken') == null
        ? this.$router.push({ name: 'Login' }).catch(() => {}) : ''
      this.$vuetify.theme.dark = true
      this.validateSession()
    },

    beforeDestroy () {
      this.$vuetify.theme.dark = false
    },
    methods: {
      validateSession () {
        const session = JSON.parse(localStorage.getItem('AccessToken'))
        // eslint-disable-next-line no-unused-expressions
        new Date().getTime() > session.expiry ? localStorage.removeItem('AccessToken') : ''
        // eslint-disable-next-line no-unused-expressions
        session == null ? this.$router.push({ name: 'Login' }).catch(() => {}) : ''
      },
    },
  }
</script>
