<template>
  <v-container
    id="data-tables"
    tag="section"
  >
    <base-material-card
      color="indigo"
      icon="mdi-book-open"
      inline
      class="px-5 py-3"
    >
      <v-btn
        color="blue darken-1"
        dark
        title="Agregar"
        absolute
        top
        right
        @click="dialog = true;"
      >
        <v-icon color="white">
          mdi-plus-circle
        </v-icon>
      </v-btn>
      <v-dialog
        v-model="dialog"
        max-width="500px"
        persistent
      >
        <v-card>
          <v-card-title>
            <span class="text-h5">{{ formTitle }}</span>
          </v-card-title>
          <v-form
            ref="form"
            v-model="valid"
            lazy-validation
          >
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.name"
                      :rules="[v => !!v || 'El Nombre de la Clase es requerido']"
                      label="Nombre"
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.classesQty"
                      type="number"
                      :rules="[v => !!v || 'La Cantidad de Clases es requerida']"
                      label="Cantidad de Clases"
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.price"
                      type="number"
                      :rules="[v => !!v || 'El Precio es requerido']"
                      label="Precio"
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                      v-model="editedItem.teacherId"
                      :items="teachers"
                      item-text="fullName"
                      item-value="id"
                      :rules="[v => !!v || 'El Instructor es requerido']"
                      label="Instructor"
                      required
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                      v-model="editedItem.categoryId"
                      :items="categories"
                      item-text="description"
                      item-value="id"
                      :rules="[v => !!v || 'La Categoria es requerida']"
                      label="Categoria"
                      required
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-file-input
                      v-model="editedItem.file"
                      label="Seleccione foto"
                    />
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer />
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                :disabled="!valid"
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-form>
        </v-card>
      </v-dialog>
      <v-dialog
        v-model="dialogDelete"
        max-width="500px"
      >
        <v-card>
          <v-card-title
            class="text-h5"
          >
            ¿Estás seguro de eliminar este registro?
          </v-card-title>
          <v-card-actions>
            <v-spacer />
            <v-btn
              color="blue darken-1"
              text
              @click="closeDelete"
            >
              Cancel
            </v-btn>
            <v-btn
              color="blue darken-1"
              text
              @click="deleteItemConfirm"
            >
              OK
            </v-btn>
            <v-spacer />
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog
        v-model="dialogDetail"
        max-width="700"
      >
        <v-card
          class="mx-auto"
          tile
        >
          <v-img
            height="300"
            :src="detailedItem.imgURL"
          >
            <v-row
              align="end"
              class="fill-height"
            >
              <v-col
                align-self="start"
                class="pa-0"
                cols="12"
              >
                <v-avatar
                  class="profile"
                  color="grey"
                  size="164"
                  tile
                >
                  <v-img :src="detailedItem.imgURL" />
                </v-avatar>
              </v-col>
              <v-col class="py-0">
                <v-list-item
                  color="rgba(0, 0, 0, .4)"
                  dark
                >
                  <v-list-item-content>
                    <v-list-item-title class="text-h2">
                      Curso
                    </v-list-item-title>
                    <v-list-item-subtitle class="text-h3 font-weight-black">
                      {{ detailedItem.name }}
                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </v-col>
            </v-row>
          </v-img>
          <v-list v-if="detailedItem.teacher != undefined">
            <v-toolbar
              color="indigo"
              dark
            >
              <v-toolbar-title>
                <v-icon
                  color="white"
                  size="50"
                >
                  mdi-teach
                </v-icon> Instructor
              </v-toolbar-title>
            </v-toolbar>
            <v-list-item>
              <v-list-item-avatar
                size="90"
              >
                <v-img
                  :src="detailedItem.teacher.imgURL"
                />
              </v-list-item-avatar>
              <v-spacer color="transparent" />
              <v-list-item-content>
                <v-list-item-title
                  class="text-h4 font-weight-bold"
                  v-text="detailedItem.teacher.fullName"
                />
              </v-list-item-content>
            </v-list-item>
            <v-toolbar
              color="indigo"
              dark
            >
              <v-toolbar-title>
                <v-icon
                  color="white"
                  size="50"
                >
                  mdi-format-list-checkbox
                </v-icon> Categoria
              </v-toolbar-title>

              <v-spacer />
            </v-toolbar>
            <v-list-item>
              <v-list-item-content>
                <v-list-item-title
                  class="text-h3 font-weight-bold text-center"
                  v-text="detailedItem.category.description"
                />
              </v-list-item-content>
            </v-list-item>
          </v-list>
          <v-divider class="mx-4" />
          <v-card-actions>
            <v-spacer />
            <v-btn
              color="blue darken-1"
              text
              @click="closeDetail"
            >
              Cerrar
            </v-btn>
            <v-spacer />
          </v-card-actions>
        </v-card>
      </v-dialog>
      <template v-slot:after-heading>
        <div class="text-h3 font-weight-light">
          Tabla de Cursos
        </div>
      </template>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        class="ml-auto"
        label="Buscar"
        hide-details
        single-line
        style="max-width: 250px;"
      />

      <v-divider class="mt-3" />

      <v-data-table
        :headers="headers"
        :items="items"
        :search.sync="search"
        :sort-by="['name', 'office']"
        :sort-desc="[false, true]"
        multi-sort
      >
        <template v-slot:item.imgURL="{item}">
          <v-avatar
            size="45"
          >
            <img
              :src="item.imgURL"
            >
          </v-avatar>
        </template>
        <template v-slot:item.actions="{ item }">
          <v-icon
            color="indigo"
            small
            class="mr-2"
            title="Detalles"
            @click="getDetails(item.id)"
          >
            mdi-eye
          </v-icon>
          <v-icon
            color="green accent-4"
            small
            class="mr-2"
            title="Editar"
            @click="editItem(item)"
          >
            mdi-pencil
          </v-icon>
          <v-icon
            small
            color="red darken-1"
            title="Eliminar"
            @click="deleteItem(item)"
          >
            mdi-delete
          </v-icon>
        </template>
      </v-data-table>
    </base-material-card>
  </v-container>
</template>

<script>
  export default {
    name: 'DashboardDataTables',

    data: () => ({
      session: JSON.parse(localStorage.getItem('AccessToken')),
      dialog: false,
      dialogDelete: false,
      dialogDetail: false,
      editedIndex: -1,
      teachers: [],
      categories: [],
      valid: false,
      select: null,
      nameRule: v => /^[a-zA-ZÀ-ÿ\u00f1\u00d1]+(\s[a-zA-ZÀ-ÿ\u00f1\u00d1]+)*[a-zA-ZÀ-ÿ\u00f1\u00d1]+$/.test(v) || 'Dato no válido',
      defaultItem: {
        name: '',
        classesQty: '',
        price: '',
        teacherId: null,
        categoryId: null,
        file: undefined,
      },
      editedItem: {
        name: '',
        classesQty: '',
        price: '',
        teacherId: null,
        categoryId: null,
        file: undefined,
      },
      detailedItem: {},
      headers: [
        {
          text: 'Imagen',
          value: 'imgURL',
        },
        {
          text: 'Nombre',
          value: 'name',
        },
        {
          text: 'Cantidad de Clases',
          value: 'classesQty',
        },
        {
          text: 'Precio',
          value: 'price',
        },
        {
          sortable: false,
          text: 'Acciones',
          value: 'actions',
        },
      ],
      items: [],
      search: undefined,
    }),
    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nuevo registro' : 'Editar Registro'
      },
    },
    async mounted () {
      await this.initialize()
    },
    methods: {
      initialize () {
        this.getCourses()
        this.getTeachers()
        this.getCategories()
      },
      getCourses () {
        fetch('https://courses-api-airb.azurewebsites.net/api/Courses', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.items = result))
      },
      getTeachers () {
        fetch('https://courses-api-airb.azurewebsites.net/api/Teachers', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.teachers = result))
      },
      getCategories () {
        fetch('https://courses-api-airb.azurewebsites.net/api/Categories', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.categories = result))
      },
      getDetails (id) {
        this.items.forEach(element => {
          if (element.id === id) {
            this.detailedItem = element
          }
        })
        this.dialogDetail = true
      },
      editItem (item) {
        fetch(`https://courses-api-airb.azurewebsites.net/api/Courses/${item.id}`, {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((response) => response.json())
          .then((course) => {
            this.editedIndex = this.items.indexOf(item)
            this.editedItem = Object.assign({}, course)
            this.dialog = true
          })
      },
      deleteItem (item) {
        this.editedIndex = this.items.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteItemConfirm () {
        fetch(`https://courses-api-airb.azurewebsites.net/api/Courses/${this.editedItem.id}`,
              {
                headers: {
                  Authorization: `Bearer ${this.session.token}`,
                },
                method: 'DELETE',
              })
          .then((response) => response.text())
          .then(() => {
            this.items.splice(this.editedIndex, 1)
            this.closeDelete()
          })
      },
      async save () {
        const updating = this.editedIndex > -1
        const id = updating ? this.editedItem.id : ''
        this.$refs.form.validate()
        if (this.valid &&
          this.editedItem.teacherId != null &&
          this.editedItem.categoryId != null) {
          const formData = new FormData()
          const data = {
            Name: this.editedItem.name,
            ClassesQty: this.editedItem.classesQty,
            Price: this.editedItem.price,
            TeacherId: this.editedItem.teacherId,
            CategoryId: this.editedItem.categoryId,
            Files: this.editedItem.file,
          }
          // eslint-disable-next-line no-unused-expressions
          updating ? formData.append('Id', id) : ''
          for (const name in data) {
            formData.append(name, data[name])
          }
          const response = await fetch(`https://courses-api-airb.azurewebsites.net/api/Courses/${id}`, {
            headers: {
              Authorization: `Bearer ${this.session.token}`,
            },
            method: updating ? 'PUT' : 'POST',
            body: formData,
          })
          console.log(response.json())
          this.editedItem = Object.assign({}, this.defaultItem)
          await this.initialize()
          this.close()
        }
      },
      close () {
        this.$refs.form.reset()
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },
      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },
      closeDetail () {
        this.dialogDetail = false
        this.$nextTick(() => {
          this.detailedItem = Object.assign({}, this.defaultItem)
        })
      },
    },
  }
</script>
