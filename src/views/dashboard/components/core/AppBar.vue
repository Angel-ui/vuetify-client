<template>
  <v-app-bar
    id="app-bar"
    absolute
    app
    color="transparent"
    flat
    height="75"
  >
    <v-btn
      class="mr-3"
      elevation="1"
      fab
      small
      @click="setDrawer(!drawer)"
    >
      <v-icon v-if="value">
        mdi-view-quilt
      </v-icon>

      <v-icon v-else>
        mdi-dots-vertical
      </v-icon>
    </v-btn>

    <v-toolbar-title
      class="hidden-sm-and-down font-weight-light"
      v-text="$route.name"
    />

    <v-spacer />

    <v-menu
      bottom
      left
      offset-y
      origin="top right"
      transition="scale-transition"
    >
      <template v-slot:activator="{ attrs, on }">
        <v-btn
          class="ml-2"
          min-width="0"
          text
          v-bind="attrs"
          v-on="on"
        >
          <v-icon size="35">
            mdi-account-circle
          </v-icon>
        </v-btn>
      </template>

      <v-list
        :tile="false"
        nav
        dark
      >
        <div class="text-center">
          <v-avatar
            size="60"
          >
            <img
              :src="session.img"
            >
          </v-avatar>
        </div>

        <div class="signed-in-as pa-5 font-weight-bold">
          Iniciaste sesión como:
          <div class="current-user blue--text font-weight-black">
            {{ session.user }}
          </div>
          Con el Rol de:
          <div
            class="current-user cyan--text font-weight-black"
            :class="session.role === 'Root'?
              'deep-orange--text text--darken-1':session.role === 'Admin'?
                'yellow--text text--darken-2':
                'light-green--text text--darken-1'"
          >
            {{ session.role }}
          </div>
        </div>
        <v-list-item @click="signOut">
          <v-icon
            size="27"
            color="red"
          >
            mdi-exit-to-app
          </v-icon>
          Cerrar sesión
        </v-list-item>
      </v-list>
    </v-menu>
  </v-app-bar>
</template>

<script>
  // Utilities
  import { mapState, mapMutations } from 'vuex'

  export default {
    name: 'DashboardCoreAppBar',

    components: {
    },

    props: {
      value: {
        type: Boolean,
        default: false,
      },
    },

    data: () => ({
      session: JSON.parse(localStorage.getItem('AccessToken')),
      notifications: [
        'Mike John Responded to your email',
        'You have 5 new tasks',
        'You\'re now friends with Andrew',
        'Another Notification',
        'Another one',
      ],
    }),

    computed: {
      ...mapState(['drawer']),
    },

    methods: {
      ...mapMutations({
        setDrawer: 'SET_DRAWER',
      }),
      signOut () {
        localStorage.removeItem('AccessToken')
        this.$router.push({ name: 'Login' })
      },
    },
  }
</script>
