<template>
  <v-layout>
    <v-flex>
      <v-data-table
        :headers="headers"
        :items="Pedido"
        :search="search"
        class="elevation-10"
      >
        <template v-slot:top>
          <v-toolbar flat color="white">
            <v-toolbar-title>Salida de la materia prima</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{}">
                <v-btn class="mx-2" fab dark color="indigo" @click="formNuevo">
                  <v-icon dark>add</v-icon>
                </v-btn>
              </template>

              <v-card>
                <v-card-title>
                  <span class="headline">Movimiento de Materia Prima</span>
                </v-card-title>

                <v-form ref="form" v-model="valid">
                  <v-card-text>
                    <v-container>
                      <v-row>
                        <v-col cols="12" sm="6">
                          <v-text-field
                            type="number"
                            v-model="NumeroPedido"
                            label="Numero de Pedido"
                            :rules="[number('numeros'),
                            maxlength('numero de pedido',6)]"
                            required
                            id="NumeroPedido"
                            @keydown="errors.clear('NumeroPedido')"
                          ></v-text-field>
                          <span
                            class="red--text"
                            v-text="errors.get('NumeroPedido')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-select
                            :items="MateriaPrima"
                            item-text="NombreMP"
                            item-value="IdRegistroMP"
                            v-model="RegistroMPID"
                            label="Selecione la materia prima"
                            :rules="[
                              (v) => !!v || 'Materia prima es requerido',
                            ]"
                            id="IdRegistroMP"
                            @click="errors.clear('IdRegistroMP')"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdRegistroMP')"
                          ></span>
                        </v-col>

                        <v-col cols="12" sm="6">
                          <v-text-field
                            type="number"
                            v-model="CantidadPedido"
                            label="Cantidad de pedido*"
                            :rules="[
                              required('cantidad de pedido'),
                              numberRule,
                            ]"
                            id="CantidadPedido"
                            @keydown="errors.clear('msg')"
                          ></v-text-field>
                          <span
                            class="red--text"
                            v-text="errors.get('msg')"
                          ></span>
                        </v-col>

                        <v-col cols="12" sm="6">
                          <v-text-field
                            v-model="DescripcionPedido"
                            label="Descripcion Pedido*"
                            counter="30"
                            :rules="[
                              required('descripcion de pedido'),
                              maxlength('Descripcion de pedido', 30),
                            ]"
                            id="DescripcionPedido"
                            @keydown="errors.clear('DescripcionPedido')"
                          >
                            <span
                              class="red--text"
                              v-text="errors.get('DescripcionPedido')"
                            ></span>
                          </v-text-field>
                        </v-col>

                        <v-col cols="12" sm="6">
                          <v-select
                            :items="Sucursal"
                            item-text="NombreSucursal"
                            item-value="IdSucursal"
                            v-model="SucursalID"
                            label="Selecione una sucursal"
                            :rules="[(v) => !!v || 'Sucursal es requerido']"
                            id="IdSucursal"
                            @click="errors.clear('IdSucursal')"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdSucursal')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-select
                            :items="Bodegas"
                            item-text="NombreBodega"
                            item-value="IdBodega"
                            v-model="BodegaID"
                            label="Selecione Bodega"
                            :rules="[(v) => !!v || 'Bodega es requerido']"
                            id="IdBodega"
                            @click="errors.clear('IdBodega')"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdBodega')"
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
                      @click="savePedido"
                      :disabled="!valid"
                      >Guardar</v-btn
                    >
                  </v-card-actions>
                </v-form>
              </v-card>
            </v-dialog>
            <!--FORMULARIO PARA EDITAR-->
            <v-dialog v-model="dialog2" max-width="500px">
              <v-card>
                <v-card-title>
                  <span class="headline"
                    >Editar Movimiento de Materia Prima</span
                  >
                </v-card-title>

                <v-form ref="form" v-model="valid">
                  <v-card-text>
                    <v-container>
                      <v-row>
                         <v-col cols="12" sm="6">
                          <v-text-field
                            type="number"
                            v-model="NumeroPedido"
                            label="Numero de Pedido"
                            :rules="[number('numeros')]"
                            required
                            disabled
                            id="NumeroPedido"
                            @keydown="errors.clear('NumeroPedido')"
                          ></v-text-field>
                          <span
                            class="red--text"
                            v-text="errors.get('NumeroPedido')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-select
                            :items="MateriaPrima"
                            item-text="NombreMP"
                            item-value="IdRegistroMP"
                            v-model="RegistroMPID"
                            label="Selecione la materia prima"
                            disabled
                            :rules="[
                              (v) => !!v || 'Materia prima es requerido',
                            ]"
                            id="IdRegistroMP"
                            @click="errors.clear('IdRegistroMP')"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdRegistroMP')"
                          ></span>
                        </v-col>

                        <v-col cols="12" sm="6">
                          <v-text-field
                            type="number"
                            v-model="CantidadPedido"
                            label="Cantidad de pedido*"
                            :rules="cantidadpedidoregla"
                            required
                            disabled
                            id="CantidadPedido2"
                            @keydown="errors.clear('CantidadPedido')"
                          ></v-text-field>
                          <span
                            class="red--text"
                            v-text="errors.get('CantidadPedido')"
                          ></span>
                        </v-col>

                        
                        <v-col cols="12" sm="6">
                          <v-text-field
                            v-model="DescripcionPedido"
                            label="Descripcion Pedido*"
                            counter="30"
                            :rules="[
                              required('descripcion de pedido'),
                              maxlength('Descripcion de pedido', 30),
                            ]"
                            id="DescripcionPedido"
                            @keydown="errors.clear('DescripcionPedido')"
                          >
                            <span
                              class="red--text"
                              v-text="errors.get('DescripcionPedido')"
                            ></span>
                          </v-text-field>
                        </v-col>

                        <v-col cols="12" sm="6">
                          <v-select
                            :items="Sucursal"
                            item-text="NombreSucursal"
                            item-value="IdSucursal"
                            disabled
                            v-model="SucursalID"
                            label="Selecione una sucursal"
                            :rules="[(v) => !!v || 'Sucursal es requerido']"
                            id="IdSucursal"
                            @click="errors.clear('IdSucursal')"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdSucursal')"
                          ></span>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-select
                            :items="Bodegas"
                            item-text="NombreBodega"
                            item-value="IdBodega"
                            disabled
                            v-model="BodegaID"
                            label="Selecione Bodega"
                            :rules="[(v) => !!v || 'Bodega es requerido']"
                            id="IdBodega"
                            @click="errors.clear('IdBodega')"
                            required
                          >
                          </v-select>
                          <span
                            class="red--text"
                            v-text="errors.get('IdBodega')"
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
                    <v-btn color="blue darken-1" text @click="saveEditar"
                      >Guardar</v-btn
                    >
                  </v-card-actions>
                </v-form>
              </v-card>
            </v-dialog>
          </v-toolbar>
          <div>
            <v-alert :value="Alert" type="success" border="top" dense>
              Registro guardado exitosamente.
            </v-alert>
            <v-alert :value="Alert2" type="success" border="top" dense>
              Registro actualizado exitosamente.
            </v-alert>
          </div>
        </template>
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon class="mb-2" color="primary" @click="formEdit(item)">
            edit
          </v-icon>
          <!-- <download-excel class="btn btn-default" :data="Pedido" worksheet="My Worksheet" name="filename.xls">
  Download Data
</download-excel> -->
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from "axios";
import jsPDF from 'jspdf';
import html2canvas from 'html2canvas';
import JsonExcel from "vue-json-excel";
import Vue from 'vue'
axios.defaults.baseURL = "http://localhost";
Vue.component("downloadExcel", JsonExcel);
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
  name: "TablePedidos",
  data() {
    return {
      Alert: false,
      Alert2: false,
      errors: new Errors(),
      valid: false,

      //Creando regla nueva para cantidad de pedido del formulario 2
      cantidadpedidoregla: [(v) => !!v || "Cantidad de pedido es necesario"],
      numberRule: (val) => {
        if (val < 0) return "Solo es permitido numeros positivo";
        return true;
      },

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

      Pedido: [],
      Bodegas: [],
      MateriaPrima: [],
      Sucursal: [],

      IdPedido: "",
      CantidadPedido: "",
      DescripcionPedido: "",
      NombreMP: "",
      RegistroMPID: "",
      NombreBodega: "",
      BodegaID: "",
      NombreSucursal: "",
      SucursalID: "",
      NumeroPedido: "",
      NombreEncargado: "",
      url4: "/PanaderiaBG/public/Sucursal",
      url3: "/PanaderiaBG/public/ShowMateriaPrima",
      url2: "/PanaderiaBG/public/Bodegas",
      url: "http://localhost/PanaderiaBG/public/Pedido",
      search: "",
      dialog: false,
      dialog2: false,

      headers: [
        {
          text: "Numero de Pedido",
          value: "NumeroPedido",
          class: "indigo  white--text",
        },
        {
          text: "Materia Prima",
          value: "NombreMP",
          class: "indigo  white--text",
        },

        {
          text: "Cantidad Pedido",
          value: "CantidadPedido",
          class: "indigo white--text",
        },

        {
          text: "Descripcion Pedido",
          value: "DescripcionPedido",
          class: "indigo white--text",
        },

        {
          text: "Sucursal",
          value: "NombreSucursal",
          class: "indigo white--text",
        },
        {
          text: "Encargado",
          value: "NombreEncargado",
          class: "indigo white--text",
        },

        {
          text: "Bodega",
          value: "NombreBodega",
          class: "indigo white--text",
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
    getMateriaPrima() {
      this.axios.get('/ShowMateriaPrima')
      .then(res=>{
        this.MateriaPrima=res.data
      })
      .catch(e=>{
        console.log(e.response)
      })
    },
    getBodegas() {
       this.axios.get('/Bodegas')
     .then(res=>{
        this.Bodegas=res.data
      })
      .catch(e=>{
        console.log(e.response)
      })
    },
    getSucursal() {
      this.axios.get('/Sucursal')
     .then(res=>{
        this.Sucursal=res.data
      })
      .catch(e=>{
        console.log(e.response)
      })
    },
    getPedido() {
      this.axios.get('/Pedido')
     .then(res=>{
        this.Pedido=res.data
      })
      .catch(e=>{
        console.log(e.response)
      })
    },

    //limpia errores front-end
    clear() {
      this.$refs.form.reset();
    },
    print() {
		const filename  = 'MovimientoMaterial.pdf';
    const quality = 1;
		html2canvas(document.querySelector('.v-data-table__wrapper'),{scale: quality}).then(canvas => {
			let pdf = new jsPDF({
  orientation: "landscape",
  unit: "in"
});

			pdf.addImage(canvas.toDataURL('image/png'), 'PNG', 0, 0, 0, 0);
			pdf.save(filename);
		});
	},

    close() {
      this.dialog = false;
      this.dialog2 = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
        this.clear();
      });
    },

    savePedido: async function () {
      const obj = new FormData();
      obj.append("CantidadPedido", this.CantidadPedido);
      obj.append("DescripcionPedido", this.DescripcionPedido);
      obj.append("RegistroMPID", this.RegistroMPID);
      obj.append("BodegaID", this.BodegaID);
      obj.append("SucursalID", this.SucursalID);
      obj.append("NumeroPedido", this.NumeroPedido);
      axios
        .post(this.url, obj)

        .then((response) => {
          //console.log(response.data.result)

          this.Pedido.push(response.data.result);
          this.CantidadPedido = "";
          this.DescripcionPedido = "";
          this.RegistroMPID = "";
          this.BodegaID = "";
          this.SucursalID = "";
           this.NumeroPedido = "";
          this.Alert = true;
          this.getPedido();
          this.clear();
          this.close();
        })
        .catch((error) => this.errors.record(error.response.data));
    },

    //Llama formulario de agregar

    formNuevo: function () {
      this.dialog = true;
    },

    //Llama formulario editar y asigna valores a cada variable

    formEdit: function (item) {
      this.CantidadPedido = item.CantidadPedido;
      this.DescripcionPedido = item.DescripcionPedido;
      this.IdPedido = item.IdPedido;
      this.RegistroMPID = item.IdRegistroMP;
      this.BodegaID = item.IdBodega;
      this.SucursalID = item.IdSucursal;
      this.NumeroPedido = item.NumeroPedido;
      this.dialog2 = true;
    },

    //Actualiza la informacion almacenada

    saveEditar: async function () {
      const obj = new FormData();

      obj.append("CantidadPedido", this.CantidadPedido);
      obj.append("DescripcionPedido", this.DescripcionPedido);
      obj.append("RegistroMPID", this.RegistroMPID);
      obj.append("BodegaID", this.BodegaID);
      obj.append("SucursalID", this.SucursalID);
      obj.append("NumeroPedido", this.NumeroPedido);
      axios
        .post(this.url + "/" + this.IdPedido, obj)

        .then((response) => {
          //console.log(response.data.result)

          this.Pedido.push(response.data.result);
          this.CantidadPedido = "";
          this.DescripcionPedido = "";
          this.RegistroMPID = "";
          this.BodegaID = "";
          this.NumeroPedido = "";
          this.Alert2 = true;
          this.getPedido();
          this.clear();
          this.close();
        })
        .catch((error) => this.errors.record(error.response.data));
    },
  },
  created() {
    this.getPedido();
    this.getMateriaPrima();
    this.getBodegas();
    this.getSucursal();
  },
};
</script>

