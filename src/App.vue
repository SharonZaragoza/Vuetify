<template>
  <v-app>
    <v-app-bar
      app
      color="dark"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

       
      </div>

      <v-spacer></v-spacer>

    
    </v-app-bar>

    

    <v-main>

    <Tabla></Tabla>

    </v-main>
  </v-app>
</template>

<script>
import Tabla from "@/components/Tabla2";


export default {
  name: 'App',

  components: {Tabla},

  data: () => ({
    //
  }),
};
</script>
