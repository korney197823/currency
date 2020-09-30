<template>
  <v-app>
    <v-main>
      <CurrentTab />
    </v-main>
  </v-app>
</template>

<script>
import CurrentTab from "./components/CurrentTab";

export default {
  name: "App",

  components: {
    CurrentTab
  },

  data: () => ({
    //
  })
};
</script>
