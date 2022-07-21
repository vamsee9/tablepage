<template>
  <v-app>
    <v-app-bar app color="orange" elevate-on-scroll collapse-on-scroll dark>
      <strong>Beers 🍻 </strong></v-app-bar
    >
    <v-main>
      <BeerList />
    </v-main>
  </v-app>
</template>

<script>
import BeerList from "./components/BeerList.vue";

export default {
  name: "App",

  components: {
    BeerList,
  },

  data: () => ({
    //
  }),
};
</script>
