<template>
  <v-data-table
    :headers="columnas"
    :items="desserts"
    class="elevation-1"
  >

    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>Catalogo Peliculas</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="primary"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              Agregar Pelicula
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

<!-- FORMULARIO PARA AGREGAR Y EDITAR -->

        <v-form ref="form" >
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                  
                    <v-text-field
                      v-model="editedItem.titulo"
                      label="Titulo"
                      :rules="validations.RulesTitulo"
                      >

                    </v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.año"
                      label="Año"
                      type="number"
                      :rules="validations.RulesAño"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.genero"
                      label="Genero"
                      :rules="validations.RulesGenero"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.director"
                      label="Director"
                      :rules="validations.RulesDirector"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      v-model="editedItem.precio"
                      label="Precio"
                      type="number"
                      :rules="validations.RulesPrecio"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>
            </v-form>
          <!-- BOTONES PARA CERRAR CUADRO DE TEXTO (CLOSE) O VALIDAR Y ENVIAR(SAVE) -->
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancelar
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
                
              >
                Guardar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <!-- Cuadro de texto para confirmar la eliminacion de un dato -->
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5"> ¿Estás seguro de que quieres eliminar? </v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cerrar</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">Eliminar</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>

    <!-- Acciones (editar/eliminar) -->
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
</template>
<script>
import  axios  from "axios";

  export default {
    data: () => ({
      dialog: false,
      dialogDelete: false,
      //Declarar campos de la tabla
      columnas: [
        { text: 'Titulo', align: 'start', sortable: false, value: 'titulo'},
        { text: 'Año', value: 'año' },
        { text: 'Genero', value: 'genero' },
        { text: 'Director', value: 'director' },
        { text: 'Precio', value: 'precio'},
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      // 'Desserts'es el arreglo a iterar en la tabla
      desserts: [],
      editedIndex: -1,
      //Valores predeterminados para la creacion/edicion de un dato
      editedItem: {
        id: 0,
        titulo : null,
        año: 0,
        genero: null,
        director: null,
        precio: 0,
      },
      defaultItem: {
        titulo : null,
        año: 0,
        genero: null,
        director: null,
        precio: 0,
      },

      //Declaracion de reglas para la validación de cada campo
      validations:{
        
        RulesTitulo: [
         v => !!v || 'Titulo Requerido',
        ],
        RulesAño:[
          v=> !!v || 'Ingresar el año',
          v=> (v<2023) || 'Favor de ingresar una fecha valida (antes del 2023)',
          v=> (v>1910) || 'Favor de ingresar una fecha valida (despues de 1910)'
        ],
        RulesGenero:[
         v => !!v || 'Genero Requerido',
        
         v => (v && v.length >= 5) || 'El genero debe tener más de 4 caracteres',

        ],
        RulesPrecio:[
          v => !!v || 'Precio Requerido'
        ],
        RulesDirector:[
          v => !!v || 'Nombre de Director requerido',
          v => (v && v.length >= 5) || 'El genero debe tener más de 4 caracteres',
        ]
    }

}),
    

    //Ciclo de inicialización/Renderizado del sitio
    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nueva Peliula' : 'Editar Pelicula'
      },
    },
    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
    },
    created () {
      this.initialize()
    },

    //CRUD
    methods: {
      //Metodo GET para llenar la tabla desde JSON-SERVER
      async initialize () {
      const respuesta = await axios.get("http://localhost:3000/peliculas");
      if (respuesta.status === 200){
        this.desserts=respuesta.data;
      }
      },

      //Guardar el index del objeto seleccionado
      editItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },


      //Mostrar alerta de eliminacion
      deleteItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      //Confirmar/Eliminar objeto seleccionado. Método DELETE
      async deleteItemConfirm () {
        const respuesta = await axios.delete(`http://localhost:3000/peliculas/${this.editedItem.id}`);
        if(respuesta.status === 200){
        this.desserts.splice(this.editedIndex, 1)}
        this.closeDelete()
      },

      //Cerrar cuadro de dialogo
      close () {
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


      //Metodos POST y PUT
      async save () {
      
      //Validacion de todo el formulario al intentar guardar/crear
      if(this.$refs.form.validate()){

        if (this.editedIndex > -1) {
        const respuesta = await axios.put(`http://localhost:3000/peliculas/${this.editedItem.id}`, this.editedItem);
        if (respuesta.status === 201 ){
        Object.assign(this.desserts[this.editedIndex], this.editedItem)
        }
        location.reload()
        } else {
          const respuesta = await axios.post("http://localhost:3000/peliculas", this.editedItem)

          if (respuesta.status === 201){
            this.editedItem.id= respuesta.data;
          this.desserts.push(this.editedItem)
        }
        }
        this.close()
        }
      },
    },
  }
</script>