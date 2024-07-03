<template>
  <div>
    <div class="container">
      <div class="row justify-content-center align-items-center borders">
        <div class="col-12 col-md-6 order-md-2">
          <div class="pokeScreen pt-4 mt-4 mb-4">
            <div class="loading-overlay">
              <p v-if="loading" class="fade-in">Escogiendo nuevo Pokémon...</p>
              <PokemonImages
                v-if="!loading"
                :id="'PokeImage'"
                :class="{ 'no-filter': filterRemoved, 'fade-in': imageLoaded }"
                :pokemonId="randomPokemonId"
                @imageLoaded="handleImageLoaded"
                @imageError="imageError"
              />
              <PokemonNames
                v-if="!loading"
                :pokemonIds="randomPokemonIds"
                :randomPokemonId="randomPokemonId"
                :selected-index="selectedPokemonIndex"
              />
              <div v-if="showPopup" class="popup-overlay" >
      <div class="popup-content">
        <h2>{{ popupMessage }}</h2>
      </div>
    </div>
            </div>

          </div>
          
        </div>

        <div class="col-8 col-md-3 btns-left text-center g-4 order-md-1 mb-4">
          <div>
            <button
              class="btnSlim"
              @click="moveSelectionUp"
              :disabled="selectedPokemonIndex === 0"
            >
              <span class="material-icons">arrow_drop_up</span>
            </button>
          </div>
          <div>
            <button>
              <span class="material-icons">arrow_left</span>
            </button>
            <button class="disabled">
              <span class="material-icons">circle</span>
            </button>
            <button>
              <span class="material-icons">arrow_right</span>
            </button>
          </div>
          <div>
            <button
              class="btnSlim"
              @click="moveSelectionDown"
              :disabled="selectedPokemonIndex === randomPokemonIds.length - 1"
            >
              <span class="material-icons">arrow_drop_down</span>
            </button>
          </div>
        </div>
        <div class="col-4 col-md-3 btns-right order-md-3">
          <button @click="selectPokemon" class="fade-in rounded-5 mt-4">
            B
          </button>
          <button @click="reloadPokemon" class="fade-in rounded-5 mb-4">
            A
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import PokemonImages from "./components/PokemonImages.vue";
import PokemonNames from "./components/PokemonNames.vue";

export default {
  components: {
    PokemonImages,
    PokemonNames,
  },
  setup() {
    const randomPokemonIds = ref([]);
    const randomPokemonId = ref(null);
    const loading = ref(true);
    const imageLoaded = ref(false);
    const imageError = ref(false);
    const pokemonId = ref(null);
    const filterRemoved = ref(false);
    const selectedPokemonIndex = ref(0);
    const showPopup = ref(false);
    const popupMessage = ref('');

    const generateRandomPokemonIds = () => {
      const randomIds = [];
      const numberOfPokemon = 4;

      while (randomIds.length < numberOfPokemon) {
        const randomId = Math.floor(Math.random() * 898) + 1;
        if (!randomIds.includes(randomId)) {
          randomIds.push(randomId);
        }
      }

      return randomIds;
    };

    const reloadPokemon = () => {
      loading.value = true;
      imageLoaded.value = false;
      imageError.value = false;
      randomPokemonIds.value = generateRandomPokemonIds();
      randomPokemonId.value =
        randomPokemonIds.value[
          Math.floor(Math.random() * randomPokemonIds.value.length)
        ];
      setTimeout(() => {
        loading.value = false;
        imageLoaded.value = true;
        filterRemoved.value = false;
      }, 1500);
    };

    const handleImageLoaded = () => {
      imageLoaded.value = true;
      imageError.value = false;
    };

    const handleImageLoadError = () => {
      imageError.value = true;
      reloadPokemon();
    };

    const selectPokemon = () => {
      const selectedPokemonId =
        randomPokemonIds.value[selectedPokemonIndex.value];
      handlePokemonSelected(selectedPokemonId);
    };

    const handlePokemonSelected = (id) => {
      console.log("Received Pokemon ID in App.vue:", id);
      console.log("Random Pokemon ID:", randomPokemonId.value);
      pokemonId.value = id;
      if (id === randomPokemonId.value) {
        popupMessage.value = 'Correcto';
        openPopup();
        filterRemoved.value = true;
      } else {
        popupMessage.value = 'Incorrecto';
        openPopup();
      }
    };

    const moveSelectionUp = () => {
      if (selectedPokemonIndex.value > 0) {
        selectedPokemonIndex.value--;
      }
    };

    const moveSelectionDown = () => {
      if (selectedPokemonIndex.value < randomPokemonIds.value.length - 1) {
        selectedPokemonIndex.value++;
      }
    };

    const openPopup = () => {
      showPopup.value = true;
      setTimeout(() => {
        showPopup.value = false;
      }, 1000); // Cierra el popup después de 2 segundos
    };

    reloadPokemon();

    return {
      randomPokemonIds,
      randomPokemonId,
      loading,
      imageLoaded,
      imageLoadError: imageError,
      pokemonId,
      filterRemoved,
      reloadPokemon,
      handleImageLoaded,
      imageError: handleImageLoadError,
      moveSelectionUp,
      moveSelectionDown,
      selectedPokemonIndex,
      selectPokemon,
      showPopup,
      openPopup,
      popupMessage,
    };
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  text-align: center;
  padding-top: 20px;
}

.loading-overlay {
  position: relative;
}

.fade-in {
  opacity: 0;
  animation: fadeInAnimation 1s forwards;
}

@keyframes fadeInAnimation {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

#PokeImage {
  filter: brightness(0) invert(0);
  transition: filter 0.5s ease;
}

.no-filter {
  filter: none !important;
}

.pokeScreen {
  min-height: 500px;
  background-color: rgb(144, 238, 137);
  border: 30px inset #3c3c3c;
}

.row {
  background-color: #f11e1e;
  border: 20px inset #ff4646;
}

.btns-left button,
.btns-right button {
  color: #dce4e9;
  background-color: #3c3c3c;
}

.btnSlim {
  width: 65px;
  height: 65px;
}

.btnRounded {
  border-radius: 50px;
}

.btns-right {
  display: flex;
  flex-direction: row;
  justify-content: center;
}

.btns-right button {
  font-size: 1.5rem;
  padding: 10px 20px;
}

.borders {
  border-radius: 80px;
}
@media screen and (max-width: 992px) {
  .borders {
    border-radius: 20px;
  }
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup-content {
  background-color:  rgb(34, 86, 30);
  color: rgb(220, 255, 218);
  padding: 20px;
  border-radius: 20px;
  width: 300px;
  text-align: center;
  position: relative;
}
</style>
