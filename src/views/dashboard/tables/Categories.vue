<template>
  <v-container
    id="data-tables"
    tag="section"
  >
    <base-material-card
      color="indigo"
      icon="mdi-format-list-checkbox"
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
                      v-model="editedItem.description"
                      :rules="[v => !!v || 'La descripción es requerida',nameRule]"
                      label="Nombre"
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
      <template v-slot:after-heading>
        <div class="text-h3 font-weight-light">
          Tabla de Categorias
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
      editedIndex: -1,
      teachers: [],
      valid: false,
      select: null,
      nameRule: v => /^[a-zA-ZÀ-ÿ\u00f1\u00d1]+(\s[a-zA-ZÀ-ÿ\u00f1\u00d1]+)*[a-zA-ZÀ-ÿ\u00f1\u00d1]+$/.test(v) || 'Dato no válido',
      defaultItem: {
        description: null,
      },
      editedItem: {
        description: null,
      },
      headers: [
        {
          text: 'Descripción',
          value: 'description',
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
        fetch('https://courses-api-airb.azurewebsites.net/api/Categories', {
          headers: {
            Authorization: `Bearer ${this.session.token}`,
          },
        })
          .then((request) => request.json())
          .then((result) => (this.items = result))
      },
      editItem (item) {
        fetch(`https://courses-api-airb.azurewebsites.net/api/Categories/${item.id}`, {
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
        fetch(`https://courses-api-airb.azurewebsites.net/api/Categories/${this.editedItem.id}`,
              {
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
        const data = {
          Description: this.editedItem.description,
        }
        console.log(data)
        if (this.valid === true && this.editedItem.description != null) {
          await fetch(`https://courses-api-airb.azurewebsites.net/api/Categories/${id}`, {
            headers: {
              Authorization: `Bearer ${this.session.token}`,
              Accept: 'application/json, text-plain',
              'Content-Type': 'application/json;charset=utf-8',
            },
            method: updating ? 'PUT' : 'POST',
            body: JSON.stringify(data),
          })
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
    },
  }
</script>
