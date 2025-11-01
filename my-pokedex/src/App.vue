<template>
  <div id="app">
    <h1>POKEDEX</h1>
    <input
      v-model="search"
      placeholder="Rechercher un Pokémon..."
      class="search"
    />

    <div class="pokemon-grid">
      <PokemonCard
        v-for="p in filteredPokemons"
        :key="p.id"
        :pokemon="p"
      />
    </div>

    <div v-if="loading" class="loading">Chargement...</div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from "vue";
import PokemonCard from "./components/PokemonCard.vue";

export default {
  components: { PokemonCard },
  setup() {
    const search = ref("");
    const pokemons = ref([]);
    const offset = ref(0);
    const limit = 20;
    const loading = ref(false);
    const totalPokemons = ref(0);

    const fetchPokemons = async () => {
      if (loading.value) return;
      loading.value = true;

      const res = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit}&offset=${offset.value}`);
      const data = await res.json();
      totalPokemons.value = data.count;

      const promises = data.results.map(async p => {
        const details = await fetch(p.url).then(res => res.json());
        const species = await fetch(details.species.url).then(res => res.json());
        details.nameFr = species.names.find(n => n.language.name === 'fr')?.name || details.name;
        details.typesList = details.types.map(t => t.type.name);
        details.cries = {
          latest: `https://raw.githubusercontent.com/PokeAPI/cries/main/cries/pokemon/latest/${details.id}.ogg`
        };
        return details;
      });

      pokemons.value.push(...(await Promise.all(promises)));
      offset.value += limit;
      loading.value = false;
    };

    const handleScroll = () => {
      if ((window.innerHeight + window.scrollY) >= document.body.offsetHeight - 500) {
        if (pokemons.value.length < totalPokemons.value) fetchPokemons();
      }
    };

    onMounted(() => {
      fetchPokemons();
      window.addEventListener('scroll', handleScroll);
    });

    const filteredPokemons = computed(() =>
      pokemons.value.filter(p =>
        p.nameFr.toLowerCase().includes(search.value.toLowerCase())
      )
    );

    return { search, filteredPokemons, loading };
  }
};
</script>

<style>
/* ========== GLOBAL ========== */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
  background: #f5f5f5;
  color: #333;
}

/* ========== HEADER ========== */
h1 {
  text-align: center;
  font-size: 2.5em;
  margin: 20px 0;
  color: #ef5350;
}

.search {
  display: block;
  margin: 0 auto 30px auto;
  padding: 10px 15px;
  font-size: 1em;
  width: 300px;
  border-radius: 10px;
  border: 1px solid #ccc;
  transition: all 0.3s;
}

.search:focus {
  outline: none;
  border-color: #ef5350;
  box-shadow: 0 0 8px rgba(239, 83, 80, 0.5);
}

/* ========== GRID ========== */
.pokemon-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 20px;
  padding: 20px;
}

@media (max-width: 1200px) {
  .pokemon-grid {
    grid-template-columns: repeat(4, 1fr);
  }
}

@media (max-width: 992px) {
  .pokemon-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (max-width: 650px) {
  .pokemon-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 400px) {
  .pokemon-grid {
    grid-template-columns: 1fr;
  }
}

/* ========== LOADING ========== */
.loading {
  text-align: center;
  font-size: 1.2em;
  margin: 20px;
}

/* ========== CARD ========== */
.pokemon-card {
  background: linear-gradient(145deg, #fff, #f0f0f0);
  border-radius: 15px;
  padding: 15px;
  text-align: center;
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
  transition: transform 0.3s, box-shadow 0.3s;
  cursor: pointer;
}

.pokemon-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0,0,0,0.2);
}

.pokemon-card img {
  width: 120px;
  height: 120px;
}

.pokemon-card h2 {
  margin: 10px 0;
  color: #ef5350;
}

.pokemon-types {
  display: flex;
  justify-content: center;
  gap: 5px;
  margin-bottom: 10px;
}

.pokemon-type {
  padding: 5px 10px;
  border-radius: 12px;
  color: white;
  font-size: 0.8em;
  text-transform: uppercase;
  font-weight: bold;
}

/* ========== COLORS PAR TYPE ========== */
.pokemon-type.normal { background: #A8A77A; }
.pokemon-type.fire { background: #EE8130; }
.pokemon-type.water { background: #6390F0; }
.pokemon-type.electric { background: #F7D02C; }
.pokemon-type.grass { background: #7AC74C; }
.pokemon-type.ice { background: #96D9D6; }
.pokemon-type.fighting { background: #C22E28; }
.pokemon-type.poison { background: #A33EA1; }
.pokemon-type.ground { background: #E2BF65; }
.pokemon-type.flying { background: #A98FF3; }
.pokemon-type.psychic { background: #F95587; }
.pokemon-type.bug { background: #A6B91A; }
.pokemon-type.rock { background: #B6A136; }
.pokemon-type.ghost { background: #735797; }
.pokemon-type.dragon { background: #6F35FC; }
.pokemon-type.dark { background: #705746; }
.pokemon-type.steel { background: #B7B7CE; }
.pokemon-type.fairy { background: #D685AD; }
</style>
