<template>
  <v-card>
    <v-card-title>
      Beers
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="headers"
      :items="Beers"
      :search="search"
    ></v-data-table>
  </v-card>
</template>

<script>
import Beers from "../assets/beers.json";
export default {

  data() {
    return {
      search: "",
      data: Beers.data,
      headers: [
        {
          text: "Brewery ID",
          value: "id",
          align: "start",
          sortable: true,
        },
        { text: "Beer name", value: "name", sortable: false },
        { text: "ABV", value: "abv", sortable: true },
        { text: "IBU", value: "ibu", sortable: true },
        { text: "SRM", value: "srm", sortable: true },
        { text: "UPC", value: "upc", sortable: true },
        { text: "Description", value: "descript", sortable: false },
      ],
      Beers: [],
    };
  },
  methods: {
    fetchTheBeer() {
      this.Beers = this.data.map(this.getBeer);
    },
    getBeer(beer) {
      return {
        id: beer.brewery_id,
        name: beer.name,
        abv: beer.abv,
        ibu: beer.ibu,
        srm: beer.srm,
        upc: beer.upc,
        descript: beer.descript,
      };
    },
  },

  mounted() {
    this.fetchTheBeer();
  },
};
</script>
