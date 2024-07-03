<template>
  <div class="pokemon-images">
    <h2>¿Quién es este pokémon?</h2>
    <div v-if="loading">Loading image...</div>
    <div v-else>
      <img
        :src="pokemon.imageUrl"
        :alt="pokemon.id"
        v-if="pokemon.imageUrl"
        @error="imageError"
      />
    </div>
  </div>
</template>

<script>
import { ref, watch, onMounted } from "vue";
import axios from "axios";

export default {
  name: "PokemonImages",
  props: {
    pokemonId: {
      type: Number,
      required: true,
    },
  },
  setup(props, { emit }) {
    const pokemon = ref({});
    const loading = ref(true);

    const fetchPokemonImage = async () => {
      loading.value = true;
      try {
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon/${props.pokemonId}`
        );
        const imageUrl = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${props.pokemonId}.svg`;
        pokemon.value = {
          id: response.data.id,
          imageUrl: imageUrl,
        };
        loading.value = false;
      } catch (error) {
        console.error("Error al cargar la imagen:", error);
        loading.value = false;
        emit("imageError");
      }
    };

    watch(
      () => props.pokemonId,
      () => {
        fetchPokemonImage();
      },
      { immediate: true }
    );

    const imageError = () => {
      pokemon.value.imageUrl = "";
      emit("imageError");
    };

    onMounted(() => {
      fetchPokemonImage();
    });

    return {
      pokemon,
      loading,
      imageError: imageError,
    };
  },
};
</script>

<style scoped>
.pokemon-images {
  text-align: center;
  padding: 20px;
}

.pokemon-images img {
  height: 150px;
  margin: 10px;
}
</style>
