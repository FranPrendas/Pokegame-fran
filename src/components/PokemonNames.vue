<template>
  <div class="btns">
    <ul
      class="list-unstyled text-right"
      :class="{ show: pokemonList.length > 0 }"
    >
      <li v-for="(pokemon, index) in pokemonList" :key="index">
        <button class="options" :class="{ selected: index === selectedIndex }">
          {{ pokemon.name }}
        </button>
      </li>
    </ul>

    <div class="info">
      <span>A Actualizar</span>
      <span>B Seleccionar</span>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    pokemonIds: {
      type: Array,
      required: true,
    },
    selectedIndex: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      pokemonList: [],
    };
  },
  watch: {
    pokemonIds: {
      handler(newIds) {
        this.fetchPokemonNames(newIds);
      },
      immediate: true,
    },
  },
  methods: {
    async fetchPokemonNames(pokemonIds) {
      this.pokemonList = [];
      for (const id of pokemonIds) {
        try {
          const response = await fetch(
            `https://pokeapi.co/api/v2/pokemon/${id}`
          );
          const data = await response.json();
          this.pokemonList.push({ id, name: data.name });
        } catch (error) {
          console.error("Error al cargar la imagen:", error);
        }
      }
    },
  },
};
</script>

<style scoped>
ul {
  list-style-type: none;
  padding: 0;
  overflow: hidden;
  max-width: 0;
  transition: max-width 0.5s ease-out, margin-left 0.5s ease-out;
  margin-left: 100%;
}

ul.show {
  max-width: 100%;
  margin-left: 0;
}

li {
  margin-bottom: 5px;
}

button {
  padding: 5px 10px;
  font-size: 14px;
  cursor: pointer;
}

.btns {
  height: 290px;
  display: flex;
  flex-direction: column;
}

.info {
  display: flex;
  justify-content: space-around;
}

.options {
  background-color: transparent;
  width: 150px;
  font-size: 18px;
  cursor: pointer;
  padding-bottom: 0px;
  margin-bottom: 0px;
}

.options.selected {
  background-color: rgb(67, 129, 62);
  color: azure;
}
.show {
  display: block;
}
</style>
