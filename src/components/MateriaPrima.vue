<template>
  <v-layout>
    <v-flex>
      <!--inicia datatable-->
      <v-data-table
        :headers="headers"
        :items="MateriaPrima"
        :search="search"
        class="elevation-10"
      >
        <template v-slot:top>
          <v-toolbar flat color="white">
            <v-toolbar-title>Materia Prima</v-toolbar-title>
            <v-divider class="mx-5" inset vertical></v-divider>
            <v-row>
              <v-col cols="3">
                <!-- Filtro por codigo -->
                <v-text-field
                  v-model="CodigoMPValue"
                  append-icon="mdi-magnify"
                  label="Codigo"
                  single-line
                  hide-details
                ></v-text-field>
              </v-col>
              <v-col cols="3">
                <!-- Filtro por materia -->
                <v-text-field
                  v-model="NombreMPValue"
                  append-icon="mdi-magnify"
                  label="Nombre"
                  single-line
                  hide-details
                ></v-text-field>
              </v-col>

              <v-col cols="3">
                <!-- Filtro por Proveedor -->
                <v-select
                  :items="Proveedor"
                  item-text="NombreProveedor"
                  v-model="ProveedorIDValue"
                  label="Proveedor"
                ></v-select>
              </v-col>
              <v-col cols="1">
                <v-btn color="blue darken-1" text @click="clearfilter"
                  >Limpiar</v-btn
                >
              </v-col>
            </v-row>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" persistent max-width="600px">
              <!---icono de agregar-->
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  class="mx-2"
                  fab
                  dark
                  color="indigo"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon dark>add</v-icon>
                </v-btn>
              </template>
              <!--- fin icono de agregar-->
              <v-card>
                <v-card-title>
                  <span class="headline">Materia prima</span>
                </v-card-title>
                <v-form ref="form" v-model="valid">
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="6">
                          <v-text-field
                            v-model="CodigoMP"
                            label="Codigo*"
                            :rules="[required('codigo')]"
                            id="CodigoMP"
                            @keydown="errors.clear('CodigoMP')"
                          >
                          </v-text-field>

                          <span
                            class="red--text"
                            v-text="errors.get('CodigoMP')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-text-field
                            v-model="NombreMP"
                            label="Nombre*"
                            :rules="[
                              required('nombre'),
                              letter('letras'),
                              minlength('Nombre', 4),
                            ]"
                            id="NombreMP"
                            @keydown="errors.clear('NombreMP')"
                          >
                            ></v-text-field
                          >

                          <span
                            class="red--text"
                            v-text="errors.get('NombreMP')"
                          ></span>
                        </v-col>

                        <v-col cols="12" sm="6">
                          <v-text-field
                            v-model="Clase"
                            label="Clase*"
                            :rules="[required('clase')]"
                            id="Clase"
                            @keydown="errors.clear('Clase')"
                          >
                            ></v-text-field
                          >

                          <span
                            class="red--text"
                            v-text="errors.get('Clase')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-text-field
                            v-model="Observacion"
                            label="Observacion*"
                            counter="30"
                            :rules="[
                              required('Observacion'),
                              maxlength('Observacion', 30),
                            ]"
                            id="Observacion"
                            @keydown="errors.clear('Observacion')"
                          >
                            >
                          </v-text-field>

                          <span
                            class="red--text"
                            v-text="errors.get('Observacion')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-text-field
                            v-model="Descripcion"
                            label="Descripcion"
                            id="Descripcion"
                            @keydown="errors.clear('Descripcion')"
                          >
                            ></v-text-field
                          >

                          <span
                            class="red--text"
                            v-text="errors.get('Descripcion')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-select
                            :items="UnidadMedida"
                            item-text="NombreUnidad"
                            item-value="IdUnidadMedida"
                            v-model="UnidadMedidaID"
                            label="Selecione la unidad"
                            id="IdUnidadMedida"
                            @click="errors.clear('IdUnidadMedida')"
                            :rules="[(v) => !!v || 'unidad es requerido']"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdUnidadMedida')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-select
                            :items="Proveedor"
                            item-text="NombreProveedor"
                            item-value="IdProveedor"
                            v-model="ProveedorID"
                            label="Selecione Proveedor"
                            id="IdProveedor"
                            @click="errors.clear('IdProveedor')"
                            :rules="[(v) => !!v || 'proveedor es requerido']"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdProveedor')"
                          ></span>
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="close"
                      >Salir</v-btn
                    >
                    <v-btn
                      color="blue darken-1"
                      text
                      @click="saveMateriaPrima"
                      :disabled="!valid"
                      >Guardar</v-btn
                    >
                  </v-card-actions>
                </v-form>
              </v-card>
            </v-dialog>

            <!--======Confirmacion eliminar=======-->

            <v-dialog v-model="dialog2" persistent max-width="350">
              <v-card>
                <v-card-title class="headline">
                  ¿Estas seguro de eliminar el registro?
                </v-card-title>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="green darken-1" text @click="saveEliminar">
                    Si
                  </v-btn>
                  <v-btn color="green darken-1" text @click="closeEliminar">
                    No
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
          <div>
            <v-alert :value="Alert" type="success" border="top" dense>
              Registro guardado exitosamente.
            </v-alert>
            <v-alert :value="Alert2" type="success" border="top" dense>
              Registro Eliminado exitosamente.
            </v-alert>
          </div>
        </template>

        <template v-slot:[`item.actions`]="{ item }">
          <v-icon
            class="mr-2"
            color="primary"
            large
            @click="FormEliminar(item)"
          >
            delete
          </v-icon>
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>
<script>
import axios from "axios";
axios.defaults.baseURL = "http://localhost";

class Errors {
  constructor() {
    this.errors = {};
  }

  get(field) {
    if (this.errors[field]) {
      return this.errors[field][0];
    }
  }

  //Guarda los errores en el array
  record(errors) {
    this.errors = errors;
  }

  //Limpia validaciones backend
  clear(field) {
    delete this.errors[field];
  }
}
export default {
  name: "TableMaPrima",
  data() {
    return {
      valid: false,
      required(propertyType) {
        return (v) =>
          (v && v.length > 0) || `Tienes que ingresar ${propertyType}`;
      },
      letter(propertyType) {
        return (v) =>
          /^[ñA-Za-z _]*[ñA-Za-z][ñA-Za-z _]+$/.test(v) ||
          `Solo acepta ${propertyType}`;
      },
      number(propertyType) {
        return (v) => /^\d+$/.test(v) || `Solo acepta ${propertyType}`;
      },
      maxlength(propertyType, maxlength) {
        return (v) =>
          (v && v.length <= maxlength) ||
          `${propertyType} no puede superar los ${maxlength} caracteres`;
      },
      minlength(propertyType, minlength) {
        return (v) =>
          (v && v.length >= minlength) ||
          `${propertyType} no puede ser inferior ${minlength} caracteres`;
      },
      dialog2: false,
      Alert: false,
      Alert2: false,
      errors: new Errors(),

      MateriaPrima: [],
      UnidadMedida: [],
      Proveedor: [],

      CodigoMP: "",
      NombreMP: "",
      Clase: "",
      Observacion: "",
      Descripcion: "",
      NombreUnidad: "",
      UnidadMedidaID: "",
      NombreProveedor: "",
      ProveedorID: "",
      alert: false,
      CodigoMPValue: "",
      NombreMPValue: "",
      ClaseValue: "",
      ObservacionValue: "",
      UnidadMedidaIDValue: null,
      ProveedorIDValue: null,
    
      urlAnular:"/PanaderiaBG/public/MateriaPrimaAnular",

      url3: "/PanaderiaBG/public/Proveedores",
      url2: "/PanaderiaBG/public/UnidadMateria",
      url: "http://localhost/PanaderiaBG/public/MateriaPrima",
      search: "",
      dialog: false,
      
      headers: [
        {
          text: "Codigo",
          align: "start",
          value: "CodigoMP",
          class: "indigo white--text",
          filter: this.CodigoFilter,
        },

        {
          text: "Nombre",
          value: "NombreMP",
          class: "indigo  white--text",
          filter: this.MateriaPrimaFilter,
        },

        {
          text: "Clase",
          value: "Clase",
          class: "indigo white--text",
          filter: this.ClaseFilter,
        },

        {
          text: "Obervacion",
          value: "Observacion",
          class: "indigo white--text",
          filter: this.ObservacionFilter,
        },
        {
          text: "Descripcion",
          value: "Descripcion",
          class: "indigo white--text",
        },
        {
          text: "Unidad de Medida",
          value: "NombreUnidad",
          class: "indigo white--text",
          filter: this.UnidadFilter,
        },
        {
          text: "Proveedor",
          value: "NombreProveedor",
          class: "indigo white--text",
          filter: this.ProveedorFilter,
        },
        {
          text: "Acción",
          value: "actions",
          class: "indigo  white--text",
          sortable: false,
        },
      ],
    };
  },
  methods: {
    getProveedores() {
      this.axios
        .get("/Proveedores")
        .then((res) => {
          this.Proveedor = res.data;
        })
        .catch((e) => {
          console.log(e.response);
        });
    },
    getUnidad() {
      this.axios
        .get("/UnidadMateria")
        .then((res) => {
          this.UnidadMedida = res.data;
        })
        .catch((e) => {
          console.log(e.response);
        });
    },
    getMateriaPrima: async function () {
      const res = await this.$http.get(this.url);
      this.MateriaPrima = res.data;
      setTimeout(() => {
        this.Alert = false;
        this.Alert2 = false;
      }, 5000);
    },
    //limpia errores front-end
    clear() {
      this.$refs.form.reset();
    },
    close() {
      this.dialog = false;
      
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
        this.clear();
      });
    },

    closeEliminar() {
      this.dialog2 = false;
    },

    FormEliminar(item) {
      this.IdRegistroMP = item.IdRegistroMP;
      this.dialog2 = true;
    },
    saveMateriaPrima: async function () {
      const obj = new FormData();
      obj.append("CodigoMP", this.CodigoMP);
      obj.append("NombreMP", this.NombreMP);
      obj.append("Clase", this.Clase);
      obj.append("Observacion", this.Observacion);
      obj.append("Descripcion", this.Descripcion);
      obj.append("UnidadMedidaID", this.UnidadMedidaID);
      obj.append("ProveedorID", this.ProveedorID);
      axios
        .post(this.url, obj)
        .then((response) => {
          this.MateriaPrima.push(response.data.result);
          this.Alert = true;
          this.CodigoMP = "";
          this.NombreMP = "";
          this.Clase = "";
          this.Observacion = "";
          this.Descripcion = "";
          this.UnidadMedidaID = "";
          this.ProveedorID = "";
          this.getMateriaPrima();
          this.close();
          this.clear();
          this.close();
        })
        .catch((error) => this.errors.record(error.response.data));
    },

    saveEliminar: async function () {
      axios
        .post(this.urlAnular + "/" + this.IdRegistroMP)
        .then(() => {
          this.Alert2 = true;
          this.getMateriaPrima();
          this.closeEliminar();
        })
        .catch((error) => this.errors.record(error.response.data));
    },

    CodigoFilter(value) {
      if (!this.CodigoMPValue) {
        return true;
      }
      return value.toLowerCase().includes(this.CodigoMPValue.toLowerCase());
    },
    MateriaPrimaFilter(value) {
      if (!this.NombreMPValue) {
        return true;
      }
      return value.toLowerCase().includes(this.NombreMPValue.toLowerCase());
    },
    ClaseFilter(value) {
      if (!this.ClaseValue) {
        return true;
      }

      return value.toLowerCase().includes(this.ClaseValue.toLowerCase());
    },
    ObservacionFilter(value) {
      if (!this.ObservacionValue) {
        return true;
      }
      return value.toLowerCase().includes(this.ObservacionValue.toLowerCase());
    },
    ProveedorFilter(value2) {
      if (!this.ProveedorIDValue) {
        return true;
      }
      return value2 === this.ProveedorIDValue;
    },
    UnidadFilter(value3) {
      if (!this.UnidadMedidaIDValue) {
        return true;
      }
      return value3 === this.UnidadMedidaIDValue;
    },
    clearfilter() {
      this.NombreMPValue = "";
      this.CodigoMPValue = "";
      this.ProveedorIDValue = "";
      this.ClaseValue = "";
      this.ObservacionValue = "";
      this.UnidadMedidaIDValue = "";
    },
  },
  created() {
    this.getMateriaPrima();
    this.getUnidad();
    this.getProveedores();
  },
};
</script>

