<template>
  <v-app>
    <center>
<v-form ref="formProducts" v-model="formProducts" lazy-validation>
    <v-col cols="12" md="4">
          <v-text-field
            v-model="product.id"
            :rules="fieldRequired"
            label="Codigo"
            :disabled="edit"
            required>
          </v-text-field>
        </v-col>

     <v-col cols="12" md="4">
          <v-text-field
            v-model="product.productsname"
            :rules="fieldRequired"
            label="Nombre del producto"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="product.description"
            :rules="fieldRequired"
            label="Descripcion"
            :counter="350"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="product.price"
            :rules="fieldRequired"
            label="Precio"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="product.category"
            :rules="fieldRequired"
            label="Categoria"
            required>
          </v-text-field>
        </v-col>

         <v-col cols="12" md="4">   
          <v-btn id="button_valid" color="success" class="mr-4"
          @click="createProducts()" v-if="!edit">Agregar</v-btn>
          <v-btn id="button_valid" color="success" class="mr-4"
          @click="editProduct()" v-else>Editar</v-btn>
        </v-col>
        
    </v-form>

     <v-data-table :headers="headers" :items="products" 
        :items-per-page="5" class="elevation-1"
        >
        
          <template v-slot:item.actions="{ item }">
            <v-icon small class="mr-2" @click="loadProduct(item)">
              mdi-pencil
            </v-icon>

            <v-icon small @click="deleteProducts(item)">
              mdi-delete
            </v-icon>
          </template>
        </v-data-table>
    </center>
  </v-app>
</template>

<script>
export default {
  name:"ProductsPage",
  beforeMount() {
    this.loadPage();
  },
  beforeUpdate() {
    try {
      this.$refs.formProducts.validate();
    } catch (error) {}
  },
  data(){
    return{
      formProducts: null,
      headers: [
        { text: "Codigo", value: "id" },
        { text: "Nombre del producto", value: "productsname" },
        { text: "Descripcion", value: "description" },
        { text: "Precio", value: "price" },
        { text: "Acciones", value: "actions" }
      ],
      products:[],
      product: {
        id: null,
        productsname: null,
        description: null,
        price: null,
        category:null,
      },
      fieldRequired: [(v) => !!v || "Rellene el campo, por favor"],
      dialog:false,
      edit: false,
    }
  },

  methods: {
      loadPage() {
      this.loadProducts();    
      },
      loadProducts() {
        let url = "http://localhost:3004/products";
        this.$axios.get(url).then((response) => {
        let data = response.data;
        this.products = data;
        });
      },
      createProducts () {
        if(this.$refs.formProducts.validate() && this.formProducts){
          let exist = this.products.find((x) => x.id == this.product.id);
          if (exist == undefined) {
          let url = "http://localhost:3004/products";
          this.$axios.post(url, this.product).then((response) => {
            this.loadProducts();
            this.product = {};
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
      loadProduct(product){
        this.product = Object.assign({}, product);
        this.edit = true;
      },
      editProduct() {
        if (this.$refs.formProducts.validate() && this.formProducts) {
        let existIndex = this.products.findIndex((x) => x.id == this.product.id);
        if (existIndex > -1) {
          let url = "http://localhost:3004/products/" + this.product.id;
          this.$axios.put(url, this.product).then((response) => {
            this.product = {};
            this.edit = false;
            this.$swal.fire(
              "Modificado.",
              "Las modificaciones al producto han sido realizadas con exito!",
              "success"
            );
            this.loadProducts();
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
    deleteProducts(product) {
      let existIndex = this.products.findIndex((x) => x.id == product.id);
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
              let url = "http://localhost:3004/products/" + product.id;
              this.$axios.delete(url).then((response) => {
                this.$swal.fire(
                  "Eliminado.",
                  "El producto ha sido eliminado.!",
                  "success"
                );
                this.loadProducts();
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
  border-color: #4caf50;
}
</style>