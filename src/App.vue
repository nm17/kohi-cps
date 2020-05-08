<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-toolbar-title>Kohi Click Test</v-toolbar-title>
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content>
      <Clicker/>
    </v-content>
  </v-app>
</template>

<script>
import Clicker from "@/components/Clicker";

export default {
  name: 'App',

  components: {
    Clicker,
  },

  data: () => ({
    //
  }),
};
</script>
