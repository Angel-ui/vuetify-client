<template>
  <v-container
    id="data-tables"
    tag="section"
  >
    <base-material-card
      color="indigo"
      icon="mdi-teach"
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
                      :rules="[v => !!v || 'El Nombre es requerido',nameRule]"
                      label="Nombre"
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.lastName"
                      :rules="[v => !!v || 'El Apellido Paterno es requerido',nameRule]"
                      label="A. Paterno"
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.maidenName"
                      :rules="[v => !!v || 'El Apellido Materno es requerido',nameRule]"
                      label="A. Materno"
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-select
                      v-model="editedItem.gender"
                      :items="genders"
                      :rules="[v => !!v || 'El género es requerido']"
                      label="Género"
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
        max-width="600"
      >
        <v-card
          class="mx-auto"
          tile
        >
          <v-img
            height="100%"
            src="https://cdn.vuetifyjs.com/images/cards/server-room.jpg"
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
                    <v-list-item-title class="text-h6">
                      Profesor
                    </v-list-item-title>
                    <v-list-item-subtitle>{{ detailedItem.fullName }}</v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </v-col>
            </v-row>
          </v-img>
          <v-toolbar
            color="indigo"
            dark
          >
            <v-toolbar-title>Cursos</v-toolbar-title>

            <v-spacer />
          </v-toolbar>
          <v-list>
            <v-list-item
              v-for="item in detailedItem.courses"
              :key="item.id"
            >
              <v-list-item-icon>
                <v-icon
                  v-if="item.icon"
                  color="pink"
                >
                  mdi-star
                </v-icon>
              </v-list-item-icon>

              <v-list-item-content>
                <v-list-item-title v-text="item.name" />
              </v-list-item-content>

              <v-list-item-avatar>
                <v-img :src="item.imgURL" />
              </v-list-item-avatar>
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
          Tabla de Profesores
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
        :sort-by="['name']"
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
      valid: false,
      select: null,
      nameRule: v => /^[a-zA-ZÀ-ÿ\u00f1\u00d1]+(\s[a-zA-ZÀ-ÿ\u00f1\u00d1]+)*[a-zA-ZÀ-ÿ\u00f1\u00d1]+$/.test(v) || 'Dato no válido',
      defaultItem: {
        gender: null,
        name: '',
        lastName: '',
        maidenName: '',
        file: undefined,
      },
      detailedItem: {},
      genders: [
        'Masculino',
        'Femenino',
      ],
      editedItem: {
        gender: null,
        name: '',
        lastName: '',
        maidenName: '',
        file: undefined,
      },
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
          text: 'Apellido Paterno',
          value: 'lastName',
        },
        {
          text: 'Apellido Materno',
          value: 'maidenName',
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
        fetch('https://courses-api-airb.azurewebsites.net/api/Teachers', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.items = result))
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
        fetch(`https://courses-api-airb.azurewebsites.net/api/Teachers/${item.id}`, {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((response) => response.json())
          .then((teacher) => {
            this.editedIndex = this.items.indexOf(item)
            this.editedItem = Object.assign({}, teacher)
            this.dialog = true
          })
      },
      deleteItem (item) {
        this.editedIndex = this.items.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },
      deleteItemConfirm () {
        fetch(`https://courses-api-airb.azurewebsites.net/api/Teachers/${this.editedItem.id}`, {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
          method: 'DELETE',
        })
          .then((response) => response.text())
          .then((result) => {
            this.items.splice(this.editedIndex, 1)
            this.closeDelete()
          })
      },
      async save () {
        const updating = this.editedIndex > -1
        const id = updating ? this.editedItem.id : ''
        this.$refs.form.validate()
        if (this.valid && this.editedItem.gender != null) {
          const formData = new FormData()
          const data = {
            Gender: this.editedItem.gender,
            Name: this.editedItem.name,
            LastName: this.editedItem.lastName,
            MaidenName: this.editedItem.maidenName,
            Files: this.editedItem.file,
          }
          // eslint-disable-next-line no-unused-expressions
          updating ? formData.append('Id', id) : ''
          for (const name in data) {
            formData.append(name, data[name])
          }
          const response = await fetch(`https://courses-api-airb.azurewebsites.net/api/Teachers/${id}`, {
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
          console.log(this.items)
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
