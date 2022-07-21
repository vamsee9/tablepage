<template>
  <v-card>
    <v-card-title>
      <v-spacer></v-spacer>
      <v-col cols="12" sm="6" md="3">
        <v-text-field
          outlined
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field
      ></v-col>
    </v-card-title>

    <!-- <v-data-table
      :headers="headers"
      :items="Beers"
      :search="search"
    ></v-data-table> -->
    <v-data-table
      fixed-header
      :headers="headers"
      :items="Beers"
      :search="search"
      sort-by="brewery_id"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar flat color="white">
          <template>
            <v-btn color="green" @click="fetchTheBeer" elevation="0" outlined>
              refresh Beers 🍻
            </v-btn>
          </template>
          <v-spacer></v-spacer>

          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="orange"
                dark
                class="mb-2"
                v-bind="attrs"
                v-on="on"
                elevation="0"
                outlined
              >
                🍺 Wanna add a beer?
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.id"
                        label="brewery ID"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.name"
                        label="Beer name"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.abv"
                        label="ABV"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.ibu"
                        label="IBU"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.srm"
                        label="SRM"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.upc"
                        label="UPC"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="50" sm="50" md="12">
                      <v-textarea
                        v-model="editedItem.descript"
                        label="Description"
                      ></v-textarea
                    ></v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="save"> Save </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >Are you sure you want to delete this beer?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Cancel</v-btn
                >
                <v-btn color="red darken-1" text @click="deleteItemConfirm"
                  >OK</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <!--eslint-disable-next-line vue/valid-v-slot-->
      <template v-slot:item.actions="{ item }">
        <v-icon color="orange" small class="mr-2" @click="editItem(item)" >
          mdi-pencil
        </v-icon>
        <v-icon color="red" small @click="deleteItem(item)">
          mdi-delete
        </v-icon>
      </template>
      
    </v-data-table>
  </v-card>
</template>

<script>
import Beers from "../assets/beers.json";
export default {
  data() {
    return {
      search: "",
      dialog: false,
      dialogDelete: false,
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
        { text: "Actions", value: "actions", sortable: false },
      ],
      Beers: [],
      editedIndex: -1,
      editedItem: {
        id: "",
        name: "",
        abv: 0,
        ibu: 0,
        srm: 0,
        upc: 0,
        descript: "",
      },
      defaultItem: {
        id: "",
        name: "",
        abv: 0,
        ibu: 0,
        srm: 0,
        upc: 0,
        descript: "",
      },
    };
  },
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "🍺 Add a Beer!" : "Edit Beer";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  created() {
    this.fetchTheBeer();
  },

  methods: {
    fetchTheBeer() {
      this.Beers = this.data.map((beer) => {
        return {
          id: beer.brewery_id,
          name: beer.name,
          abv: beer.abv,
          ibu: beer.ibu,
          srm: beer.srm,
          upc: beer.upc,
          descript: beer.descript,
        };
      });
    },
    // mounted() {
    //   this.fetchTheBeer();
    // },

    editItem(item) {
      this.editedIndex = this.Beers.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    deleteItem(item) {
      this.editedIndex = this.Beers.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },
    deleteItemConfirm() {
      this.Beers.splice(this.editedIndex, 1);
      this.closeDelete();
    },
    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.Beers[this.editedIndex], this.editedItem);
      } else {
        this.Beers.push(this.editedItem);
      }
      this.close();
    },
  },
};
</script>
