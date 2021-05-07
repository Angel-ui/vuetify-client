<template>
  <v-container
    id="login"
    class="fill-height justify-center"
    tag="section"
  >
    <v-row justify="center">
      <v-slide-y-transition appear>
        <base-material-card
          color="success"
          light
          max-width="100%"
          width="400"
          class="px-5 py-3"
        >
          <template v-slot:heading>
            <div class="text-center">
              <h1 class="text-h3 font-weight-bold mb-2">
                Iniciar Sesión
              </h1>
            </div>
          </template>
          <v-form
            ref="form"
            v-model="valid"
            lazy-validation
            @keyup.enter="save"
          >
            <div v-show="error">
              <v-alert
                dense
                outlined
                type="error"
              >
                <strong>Credenciales no válidas</strong>
              </v-alert>
            </div>
            <v-card-text class="text-center">
              <v-text-field
                v-model="emailAddress"
                :rules="emailRules"
                color="secondary"
                label="Correo Electrónico..."
                prepend-icon="mdi-email"
              />

              <v-text-field
                v-model="password"
                :rules="passwordRules"
                type="password"
                class="mb-8"
                color="secondary"
                label="Contraseña..."
                prepend-icon="mdi-lock-outline"
              />

              <pages-btn
                large
                color=""
                depressed
                class="v-btn--text success--text"
                :disabled="!valid"
                @click="save"
              >
                Let's Go
              </pages-btn>
            </v-card-text>
          </v-form>
        </base-material-card>
      </v-slide-y-transition>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    name: 'PagesLogin',

    components: {
      PagesBtn: () => import('./components/Btn'),
    },

    data: () => ({
      error: false,
      emailAddress: null,
      password: null,
      valid: false,
      passwordRules: [
        v => !!v || 'Contraseña obligatoria',
        v => (v && v.length >= 4) || 'La contraseña debe tener al menos 4 caracteres',
      ],
      emailRules: [
        v => !!v || 'Correo electrónico obligatorio',
        v => /.+@.+\..+/.test(v) || 'Correo electrónico no válido',
      ],
      socials: [
        {
          href: '#',
          icon: 'mdi-facebook-box',
        },
        {
          href: '#',
          icon: 'mdi-twitter',
        },
        {
          href: '#',
          icon: 'mdi-github-box',
        },
      ],
    }),
    created () {
      this.validateSession()
    },
    methods: {
      validateSession () {
        const session = JSON.parse(localStorage.getItem('AccessToken'))
        // eslint-disable-next-line no-unused-expressions
        session != null ? this.$router.push({ name: 'Dashboard' }).catch(() => {}) : ''
      },
      save () {
        const router = this.$router
        this.$refs.form.validate()
        const data = { emailAddress: this.emailAddress, password: this.password, firstName: 'rfd', middleName: 'dfdf', lastName: 'dfdf', roleId: 0 }
        if (this.valid === true && this.emailAddress != null && this.password != null) {
          fetch('https://courses-api-airb.azurewebsites.net/api/Users/Login',
                {
                  headers: {
                    Accept: 'application/json, text-plain',
                    'Content-Type': 'application/json;charset=utf-8',
                  },
                  method: 'POST',
                  // eslint-disable-next-line comma-dangle
                  body: JSON.stringify(data),
                },
          )
            .then((response) => {
              if (response.ok) {
                return response.json()
              } else {
                this.error = true
              }
            })
            .then((value) => {
              if (value.accessToken != null) {
                const obj = {
                  user: `${value.firstName} ${value.middleName} ${value.lastName}`,
                  token: value.accessToken,
                  role: value.role.roleDesc,
                  expiry: new Date().getTime() + 14000 * 1000,
                  img: value.imgURL,
                }
                localStorage.setItem('AccessToken', JSON.stringify(obj))
                router.push({ name: 'Dashboard' })
              }
            })
            .catch((err) => {
              console.log(err)
            })
        }
      },
    },
  }
</script>
