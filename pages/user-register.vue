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
          @click:append="showPassword = !showPassword"
        ></v-text-field>
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
          v-model="user.tidentification_type"
          :rules="fieldRequired"
          label="Tipo de identificacion"
          required>
        ></v-select>
      </v-col>

      <v-col cols="12" md="4">
        <v-text-field
          v-model="user.identification"
          :rules="fieldRequired"
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
        ></v-select>
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
        <v-btn id="button_valid"
          color="success"
          class="mr-4"
          @click="validate">
          Aceptar
        </v-btn>

        <v-btn id="button_reset"
          color="error"
          class="mr-4"
          @click="reset">
          Restablecer formulario
        </v-btn>
      </v-col>

     </v-form>
     </center>
</v-app>
</template>

<script>
  export default {
    data () {
      return{
      showPassword: false,
      formRegister: null,
      user: {
        id: null,
        firstname: null,
        lastname: null,
        age:null,
        country:null,
        city:null,
        identification_type: null,
        identification:null,
        email: null,
        password: null,
        type_user: null
      },
      identification_types:[
        {id:"01", name: "Cedula de ciudadania"},
        {id:"02", name: "Cedula de extranjeria"},
        {id:"03", name: "Pasaporte"},
        {id:"04", name: "Tarjeta identidad"}
      ],
      user_type: ['Cliente', 'Proveedor'],
      fieldRequired: [(v) => !!v || "Rellene el campo, por favor"],
      emailRules: [
        (v) => !!v || "El correo electronico es requerido",
        (v) => /.+@.+/.test(v) || "El correo electronico debe ser valido",
      ],
      select:null,
      checkbox: false,
      }
    },

    methods: {
      validate () {
        this.$refs.formRegister.validate() 
      },
      reset () {
        this.$refs.formRegister.reset()
      },
    },
  }
</script> 

<style>
#button_valid{
  background-color:#4caf50;
  border-color: #4caf50;
}

#button_reset{
  background-color: #ff5252;
  border-color: #ff5252;
  margin-top: 15px;
}
</style>