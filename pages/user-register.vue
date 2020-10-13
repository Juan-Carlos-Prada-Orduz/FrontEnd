<template>
  <v-app>
    <center>
      <v-form ref="formRegister" v-model="formRegister" lazy-validation>
   
        <h1 color="primary">Crea cuenta</h1>
      
        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.firstname"
            :rules="fieldRequired"
            label="Nombre"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.lastname"
            :rules="fieldRequired"
            label="Apellidos"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.email"
            :rules="emailRules"
            label="Correo"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.password"
            :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
            :rules="[v => !!v || 'Debe ingresar una contraseña']"
            :type="showPassword ? 'text' : 'password'"
            name="input-10-1"
            label="Contraseña"
            @click:append="showPassword = !showPassword">
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.age"
            :rules="fieldRequired"
            label="Edad"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.country"
            :rules="fieldRequired"
            label="País"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.city"
            :rules="fieldRequired"
            label="Ciudad"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-select
            :items="identification_types"
            item-value="id"
            item-text="name"
            v-model="user.identification_type"
            :rules="fieldRequired"
            label="Tipo de identificacion"
            required>
          </v-select>
        </v-col>

        <v-col cols="12" md="4">
          <v-text-field
            v-model="user.id"
            :rules="fieldRequired"
            :disabled="edit"
            label="Identificacion"
            required>
          </v-text-field>
        </v-col>

        <v-col cols="12" md="4">
          <v-select
            :items="user_type"
            item-value="id"
            item-text="name"
            :rules="fieldRequired"
            v-model="user.type_user"
            label="Tipo de usuario"
            required>
          </v-select>
        </v-col>

        <v-col cols="12" md="4">
          <v-checkbox
            v-model="checkbox"
            :rules="[v => !!v || '¡Debes aceptar para continuar!']"
            label="¿Aceptas terminos y condiciones?"
            required>
          </v-checkbox>
        </v-col>

        <v-col cols="12" md="4">   
          <v-btn id="button_valid" color="success" class="mr-4"
          @click="createUser()" v-if="!edit">Aceptar</v-btn>
        <v-btn id="button_valid" color="success" class="mr-4"
          @click="editUser()" v-else>Editar</v-btn>
        </v-col>

      </v-form>

        <v-data-table :headers="headers" :items="users" 
        :items-per-page="5" class="elevation-1"
        >

          <template v-slot:item.actions="{ item }">
            <v-icon small class="mr-2" @click="loadUser(item)">
              mdi-pencil
            </v-icon>

            <v-icon small @click="deleteUser(item)">
              mdi-delete
            </v-icon>
          </template>
        </v-data-table>

        <v-dialog v-model="dialog" max-width="290">
          <v-card>
            <v-card-title class="headline">
              Error
            </v-card-title>

            <v-card-text>
              Por favor, rellene todos los campos
            </v-card-text>
        
            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn color="accent" text @click="dialog = false">
                Cerrar
              </v-btn>
            </v-card-actions>
          </v-card>
      </v-dialog>
    </center>
  </v-app>
</template>

<script>
  export default {
    name: "RegisterPage",
    beforeMount() {
      this.loadPage();
    },
    beforeUpdate() {
      try {
        this.$refs.formRegister.validate();
      } catch (error) {}
    },
    data () {
      return{
      showPassword: false,
      formRegister: null,
      headers: [
        { text: "Identification", value: "id" },
        { text: "Nombre", value: "firstname" },
        { text: "Correo", value: "email" },
        { text: "Tipo usuario", value: "type_user" },
        { text: "Acciones", value: "actions" },
      ],
      users:[],
      user: {
        id: null,
        firstname: null,
        lastname: null,
        age:null,
        country:null,
        city:null,
        identification_type: null,
        email: null,
        password: null,
        type_user: null
      },
      identification_types:[],
      user_type: ['Cliente', 'Proveedor'],
      fieldRequired: [(v) => !!v || "Rellene el campo, por favor"],
      emailRules: [
        (v) => !!v || "El correo electronico es requerido",
        (v) => /.+@.+/.test(v) || "El correo electronico debe ser valido",
      ],
      select:null,
      checkbox: false,
      dialog:false,
      edit: false,
      }
    },

    methods: {
      loadPage() {
      this.loadUsers();    
      this.loadIdenticationTypes();
      },
      loadIdenticationTypes() {
      let url = "http://localhost:3004/identification_types";
      this.$axios.get(url).then((response) => {
        let data = response.data;
        this.identification_types = data;
        });
      },
      loadUsers() {
        let url = "http://localhost:3004/users";
        this.$axios.get(url).then((response) => {
        let data = response.data;
        this.users = data;
        });
      },
      createUser() {
        if(this.$refs.formRegister.validate() && this.formRegister){
          let exist = this.users.find((x) => x.id == this.user.id);
          if (exist == undefined) {
          let url = "http://localhost:3004/users";
          this.$axios.post(url, this.user).then((response) => {
            this.loadUsers();
            this.user = {};
            this.$swal.fire(
              "Creado con exito",
              "El usuario ha sido creado correctamente.",
              "success"
              );
            });
          }else{
            alert("El usuario ya existe");
            /**Swal.fire({
            icon: "error",
            title: "Oops...",
            text: "El usuario ya existe.",
            });*/
          }
        }else{
          this.dialog = true;
        }
      },
      loadUser(user){
        this.user = Object.assign({}, user);
        this.edit = true;
      },
      editUser() {
        if (this.$refs.formRegister.validate() && this.formRegister) {
        let existIndex = this.users.findIndex((x) => x.id == this.user.id);
        if (existIndex > -1) {
          let url = "http://localhost:3004/users/" + this.user.id;
          this.$axios.put(url, this.user).then((response) => {
            this.user = {};
            this.edit = false;
            this.$swal.fire(
              "Modificado.",
              "El usuario ha sido modificada correctamente.",
              "success"
            );
            this.loadUsers();
          });
        } else {
          this.$swal.fire({
            icon: "error",
            title: "Oops...",
            text: "La persona NO existe.",
          });
        }
      } else {
        this.dialog = true;
      }
    },
    deleteUser(user) {
       console.log(user);
      let existIndex = this.users.findIndex((x) => x.id == user.id);
      if (existIndex > -1) {
        this.$swal
          .fire({
            title: "¿Esta seguro que desea eliminar este usuario?",
            type: "warning",
            showCancelButton: true,
            confirmButtonColor: "#2B8A28",
            cancelButtonColor: "#d33",
            confirmButtonText: "Eiminar",
            cancelButtonText: "Cancelar",
          })
          .then((result) => {
            console.log(result);
            if (result.value) {
              let url = "http://localhost:3004/users/" + user.id;
              this.$axios.delete(url).then((response) => {
                this.$swal.fire(
                  "Eliminado.",
                  "El usuario ha sido eliminada correctamente.!",
                  "success"
                );
                this.loadUsers();
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