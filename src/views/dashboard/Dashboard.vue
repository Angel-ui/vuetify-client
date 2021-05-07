<template>
  <v-container
    id="dashboard"
    tag="section"
  >
    <v-row>
      <v-col
        cols="12"
      >
        <div
          class="font-weight-light mt-1"
          style="font-size: 30px"
        >
          Manejo de registros
        </div>
      </v-col>

      <v-col
        sm="12"
        md="6"
      >
        <base-material-card
          color="transparent"
          image
          hover-reveal
        >
          <template v-slot:image>
            <v-img
              :src="require(`@/assets/${'courses.png'}`)"
            />
          </template>
          <v-card-title class="justify-center font-weight-bold">
            Cursos
          </v-card-title>

          <v-card-text class="text-body-1 text-center mb-3 font-weight-light grey--text">
            Administrar los cursos, incluyendo asignación de profesores.
          </v-card-text>

          <template v-slot:actions>
            <div class="text-h3 font-weight-light grey--text">
              Cursos: {{ coursesCount }}
            </div>

            <v-spacer />

            <v-btn
              small
              color="blue darken-1"
              @click="$router.push( {name:'Courses'} )"
            >
              Ir a Cursos
            </v-btn>
          </template>
        </base-material-card>
      </v-col>

      <v-col
        sm="12"
        md="6"
      >
        <base-material-card
          color="transparent"
          hover-reveal
          image
        >
          <template v-slot:image>
            <v-img
              :src="require(`@/assets/${'teacher.png'}`)"
            />
          </template>

          <v-card-title class="justify-center font-weight-bold">
            Profesores
          </v-card-title>

          <v-card-text class="text-body-1 text-center mb-3 font-weight-light grey--text">
            Administrar los profesores, se puede acceder a la lista de cursos.
          </v-card-text>

          <template v-slot:actions>
            <div class="text-h3 font-weight-light grey--text">
              Profesores: {{ teachersCount }}
            </div>

            <v-spacer />

            <v-btn
              small
              color="blue darken-1"
              @click="$router.push( {name:'Teachers'} )"
            >
              Ir a Profesores
            </v-btn>
          </template>
        </base-material-card>
      </v-col>
      <v-col
        sm="12"
        md="6"
      >
        <base-material-card
          color="transparent"
          hover-reveal
          image
        >
          <template v-slot:image>
            <v-img
              :src="require(`@/assets/${'categories.png'}`)"
            />
          </template>
          <v-card-title class="justify-center font-weight-bold">
            Categorias
          </v-card-title>
          <v-card-text class="text-body-1 text-center mb-3 font-weight-light grey--text">
            Administrar las categorias para asignar a los cursos del sistema.
          </v-card-text>

          <template v-slot:actions>
            <div class="text-h3 font-weight-light grey--text">
              Categorias: {{ categoriesCount }}
            </div>

            <v-spacer />

            <v-btn
              small
              color="blue darken-1"
              @click="$router.push( {name:'Categories'} )"
            >
              Ir a Categorias
            </v-btn>
          </template>
        </base-material-card>
      </v-col>
      <v-col
        sm="12"
        md="6"
      >
        <base-material-card
          color="transparent"
          hover-reveal
          image
        >
          <template v-slot:image>
            <v-img
              :src="require(`@/assets/${'user.png'}`)"
            />
          </template>
          <v-card-title class="justify-center font-weight-bold">
            Usuarios
          </v-card-title>
          <v-card-text class="text-body-1 text-center mb-3 font-weight-light grey--text">
            Administrar los usuarios (solo para usuarios con rol de administrador).
          </v-card-text>

          <template
            v-if="session.role == 'Admin' || session.role == 'Root'"
            v-slot:actions
          >
            <div class="text-h3 font-weight-light grey--text">
              Usuarios: {{ usersCount }}
            </div>

            <v-spacer />

            <v-btn
              small
              color="blue darken-1"
              @click="$router.push( {name:'Users'} )"
            >
              Ir a Usuarios
            </v-btn>
          </template>
          <template
            v-else
            v-slot:actions
          >
            <div class="text-h3 font-weight-light grey--text">
              No autorizado
            </div>

            <v-spacer />

            <span class="text-caption grey--text font-weight-light" />
          </template>
        </base-material-card>
      </v-col>
    </v-row>
    <br>
    <br>
    <br>
    <br>
  </v-container>
</template>

<script>
  export default {
    name: 'DashboardDashboard',

    data: () => ({
      session: JSON.parse(localStorage.getItem('AccessToken')),
      categoriesCount: 0,
      coursesCount: 0,
      usersCount: 0,
      teachersCount: 0,
    }),
    mounted () {
      this.initialize()
    },
    methods: {
      initialize () {
        this.getTeachersCount()
        this.getCategoriesCount()
        this.getCoursesCount()
        if (this.session.role === 'Admin' || this.session.role === 'Root') {
          this.getUsersCount()
        }
      },
      getTeachersCount () {
        fetch('https://courses-api-airb.azurewebsites.net/api/Teachers', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.teachersCount = result.length))
      },
      getCategoriesCount () {
        fetch('https://courses-api-airb.azurewebsites.net/api/Categories', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.categoriesCount = result.length))
      },
      getUsersCount () {
        fetch('https://courses-api-airb.azurewebsites.net/api/Users', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.usersCount = result.length))
      },
      getCoursesCount () {
        fetch('https://courses-api-airb.azurewebsites.net/api/Courses', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.coursesCount = result.length))
      },
    },
  }
</script>
