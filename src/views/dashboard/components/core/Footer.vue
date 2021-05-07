<template>
  <v-footer
    id="dashboard-core-footer"
    :absolute="$vuetify.breakpoint.name !== 'xs' && $vuetify.breakpoint.name !== 'sm'"
  >
    <v-container>
      <v-row
        align="center"
        no-gutters
      >
        <v-spacer class="hidden-sm-and-down" />

        <v-col
          cols="12"
          md="auto"
        >
          <div class="text-body-1 font-weight-light pt-6 pt-md-0 text-center">
            &copy; {{ new Date().getFullYear() }}, realizado con Vue-Vuetify-NETCore
            <v-icon size="18">
              mdi-heart
            </v-icon>
            by <a href="https://github.com/Angel-ui">Ángel Rodríguez</a>
          </div>
        </v-col>
      </v-row>
    </v-container>
  </v-footer>
</template>

<script>
  export default {
    name: 'DashboardCoreFooter',

    data: () => ({
    }),
  }
</script>

<style lang="sass">
  #dashboard-core-footer
    a
      font-size: .825rem
      font-weight: 500
      text-decoration: none
      text-transform: uppercase
</style>
