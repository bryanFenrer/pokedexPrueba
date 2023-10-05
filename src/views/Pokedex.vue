<template>
  <div>
    <h1 class="text-h1">Pokedex</h1>

    <v-container>
      <v-row>
        <v-col cols="12" md="8" lg="6">
          <v-text-field v-model="searchQuery" label="Buscar por nombre o número de Pokémon" @keyup.enter="searchPokemon"
            outlined></v-text-field>
          <v-btn @click="searchPokemon" color="primary">Buscar</v-btn>
        </v-col>
      </v-row>
    </v-container>
    <v-container>
      <v-row>
        <v-col v-for="pokemon in paginatedPokemons" :key="pokemon.id" cols="12" sm="6" md="4" lg="3">
          <v-card class="pa-3 ma-3" elevation="2" @click="showPokemonDetails(pokemon)">
            <v-img :src="pokemon.image" aspect-ratio="1">
              <v-row class="fill-height ma-0" align="end">
                <v-col>
                  <v-chip :color="getTypeColor(pokemon.type)" class="white--text">
                    {{ pokemon.type }}
                  </v-chip>
                </v-col>
              </v-row>
            </v-img>
            <v-card-title class="text-h6">
              {{ pokemon.name }}
            </v-card-title>
          </v-card>
        </v-col>
      </v-row>
    </v-container>

    <v-dialog v-model="dialog" max-width="600" content-class="dialog-margin">
      <v-card>
        <v-img :src="selectedPokemon.image" aspect-ratio="1" style="max-width: 35%">
          <v-row class="fill-height ma-0" align="end">
            <v-col>
              <v-chip :color="getTypeColor(selectedPokemon.type)" class="white--text">
                {{ selectedPokemon.type }}
              </v-chip>
            </v-col>
          </v-row>
        </v-img>
        <v-card-title class="text-h6">
          {{ selectedPokemon.name }}
        </v-card-title>
        
        <v-list dense>
          <v-subheader>Estadísticas</v-subheader>
          <v-list-item-group v-if="selectedPokemon.stats">
            <v-list-item v-for="(stat, index) in selectedPokemon.stats" :key="index">
              <v-row>
                <v-col class="text-capitalize" cols="6">{{ stat.stat.name }}:</v-col>
                <v-col cols="6">
                  <v-progress-linear height="15" color="primary" v-model="stat.base_stat">
                      <template v-slot:default="{ value }">
                        <strong>{{ value }}</strong>
                      </template>
                    </v-progress-linear>
                </v-col>
              </v-row>
            </v-list-item>
          </v-list-item-group>
        </v-list>

        <v-card-actions>
          <v-spacer></v-spacer>
        <v-btn @click="dialog = false" color="red darken-2">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-pagination
      v-if="totalPages > 1"
      v-model="currentPage"
      :length="totalPages"
      @input="changePage"
    ></v-pagination>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'PokedexPage',
  data() {
    return {
      pokemons: [],
      dialog: false,
      selectedPokemon: null,
      searchQuery: '',
      allPokemons: [],
      paginatedPokemons: [],
      currentPage: 1,
      itemsPerPage: 20,
    }
  },
  created() {
    this.fetchPokemons()
  },
  computed: {
    totalPages() {
      return Math.ceil(this.allPokemons.length / this.itemsPerPage)
    },
  },
  methods: {
    async fetchPokemons() {
      try {
        const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=10000')
        const pokemonData = await Promise.all(
          response.data.results.map(async (pokemon) => {
            const detailsResponse = await axios.get(pokemon.url)
            return {
              id: detailsResponse.data.id,
              name: pokemon.name,
              image: detailsResponse.data.sprites.front_default,
              type: detailsResponse.data.types[0].type.name,
              stats: detailsResponse.data.stats,
            }
          })
        )
        this.allPokemons = pokemonData
        this.changePage(1)
      } catch (error) {
        console.error('Error fetching Pokemon data:', error)
      }
    },

    getTypeColor(type) {
      const typeColors = {
        normal: 'grey',
        fire: 'red darken-2',
        water: 'blue darken-1',
        electric: 'yellow darken-2',
        grass: 'green',
        ice: 'light-blue lighten-1',
        fighting: 'red darken-3',
        poison: 'purple darken-2',
        ground: 'brown darken-2',
        flying: 'blue lighten-2',
        psychic: 'purple darken-1',
        bug: 'green darken-1',
        rock: 'brown darken-1',
        ghost: 'indigo darken-4',
        dark: 'black',
        dragon: 'deep-purple darken-3',
        steel: 'blue-grey darken-3',
        fairy: 'pink lighten-2',
      }

      return typeColors[type] || 'grey'
    },

    showPokemonDetails(pokemon) {
      this.selectedPokemon = pokemon
      this.dialog = true
    },

    async searchPokemon() {
      const searchTerm = this.searchQuery.toLowerCase()

      try {
        const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${searchTerm}`)
        const pokemonData = {
          id: response.data.id,
          name: response.data.name,
          image: response.data.sprites.front_default,
          type: response.data.types[0].type.name,
          stats: response.data.stats,
        }

        this.selectedPokemon = pokemonData
        this.dialog = true

        // Actualizar la lista de Pokémon filtrados
        this.filteredPokemons = [pokemonData]
      } catch (error) {
        alert('Pokémon no encontrado.')
      }
    },

    async changePage(page) {
      const startIndex = (page - 1) * this.itemsPerPage
      
      try {
        const response = await axios.get(`https://pokeapi.co/api/v2/pokemon?offset=${startIndex}&limit=${this.itemsPerPage}`)
        const pokemonData = await Promise.all(
          response.data.results.map(async (pokemon) => {
            const detailsResponse = await axios.get(pokemon.url)
            return {
              id: detailsResponse.data.id,
              name: pokemon.name,
              image: detailsResponse.data.sprites.front_default,
              type: detailsResponse.data.types[0].type.name,
              stats: detailsResponse.data.stats,
            }
          })
        )
        this.paginatedPokemons = pokemonData
        this.currentPage = page
      } catch (error) {
        console.error('Error fetching Pokemon data:', error)
      }
    },
  },
  watch:{
    currentPage(val) {
      this.changePage(val)
    }
  }
}
</script>

<style scoped>
.text-h1 {
  text-align: center;
}

.card {
  width: 80%;
  margin: 2vh;
}

.dialog-margin {
  margin: 5%;
}
</style>