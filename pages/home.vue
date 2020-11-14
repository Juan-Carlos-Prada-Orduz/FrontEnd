<template>
  <v-app>
    <center>
<v-form ref="formVentas" v-model="formVentas" lazy-validation>

    <v-col cols="12" md="4">
          <v-text-field
            v-model="sale.id"
            :rules="fieldRequired"
            label="id"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="sale.id_usuario"
            :rules="fieldRequired"
            label="id usuario"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="sale.tipo_venta"
            :rules="fieldRequired"
            label="tipo venta"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">   
          <v-btn id="button_valid" color="success" class="mr-4"
          @click="createSales()" v-if="!edit">Aceptar</v-btn>
        <v-btn id="button_valid" color="success" class="mr-4"
          @click="editSale()" v-else>Editar</v-btn>
        </v-col>


    </v-form>



     <v-data-table :headers="headers" :items="sales" 
        :items-per-page="5" class="elevation-1"
        >
        
          <template v-slot:item.actions="{ item }">
            <v-icon small class="mr-2" @click="loadSale(item)">
              mdi-store
            </v-icon>

          </template>
        </v-data-table>
    </center>
  </v-app>
</template>

<script>
export default {
  name:"SalesPage",
  beforeMount() {
    this.loadPage();
  },
  beforeUpdate() {
    try {
      this.$refs.formVentas.validate();
    } catch (error) {}
  },
  data(){
    return{
      formVentas: null,
      headers: [
        { text: "id", value: "id" },
        { text: "id usuario", value: "id_usuario" },
        { text: "tipo venta", value: "tipo_venta" }
      ],
      sales:[],
      sale: {
        id: null,
        id_usuario: null,
        tipo_venta: null
      },
      fieldRequired: [(v) => !!v || "Rellene el campo, por favor"],
      dialog:false,
      edit: false,
    }
  },

  methods: {
      loadPage() {
      this.loadSales();    
      },
      loadSales() {
        let url = "http://localhost:3007/api/v1/ventas";
        this.$axios.get(url).then((response) => {
        let data = response.data;
        this.sales = data;
        });
      },
      createSales () {
        if(this.$refs.formVentas.validate() && this.formVentas){
          let exist = this.sales.find((x) => x.id == this.sale.id);
          if (exist == undefined) {
          let url = "http://localhost:3007/api/v1/ventas";
          this.$axios.post(url, this.sale).then((response) => {
            this.loadSales();
            this.sale = {};
            this.$swal.fire(
              "Creado con exito",
              "Se ha agregado el producto con exito",
              "success"
              );
            });
          }else{
            Swal.fire({
            icon: "error",
            title: "Oops...",
            text: "Ya existe un producto con ese codigo.",
            });
          }
        }else{
          this.dialog = true;
        }
      },
      loadSale(sale){
        this.sale = Object.assign({}, sale);
        this.edit = true;
      },
      editSale() {
        if (this.$refs.formVentas.validate() && this.formVentas) {
        let existIndex = this.sales.findIndex((x) => x.id == this.sale.id);
        if (existIndex > -1) {
          let url = "http://localhost:3007/api/v1/ventas" + this.sale.id;
          this.$axios.put(url, this.sale).then((response) => {
            this.sale = {};
            this.edit = false;
            this.$swal.fire(
              "Modificado.",
              "Las modificaciones al producto han sido realizadas con exito!",
              "success"
            );
            this.loadSales();
          });
        } else {
          this.$swal.fire({
            icon: "error",
            title: "Oops...",
            text: "El producto no existe.",
          });
        }
      } else {
        this.dialog = true;
      }
    },
    deleteSales(sale) {
      let existIndex = this.sales.findIndex((x) => x.id == sale.id);
      if (existIndex > -1) {
        this.$swal
          .fire({
            title: "¿Desea eliminar este producto?",
            text: "Este cambio no se puede revertir.",
            type: "warning",
            showCancelButton: true,
            confirmButtonColor: "#2B8A28",
            cancelButtonColor: "#d33",
            confirmButtonText: "Eliminar",
            cancelButtonText: "Cancelar",
          })
          .then((result) => {
            if (result.value) {
              let url = "http://localhost:3007/api/v1/ventas" + sale.id;
              this.$axios.delete(url).then((response) => {
                this.$swal.fire(
                  "Eliminado.",
                  "El producto ha sido eliminado.!",
                  "success"
                );
                this.loadSales();
              });
            }
          });
      } else {
        this.$swal.fire({
          icon: "error",
          title: "Oops...",
          text: "La persona NO existe en la tabla.",
        });
      }
    }
  }
}
</script>

<style>
#button_valid{
  background-color:#4caf50;
  border-color: #000000;
}
</style>