<template>
  <div class="pokemon-card" @click="playCry">
    <img :src="pokemon.sprites.front_default" :alt="pokemon.nameFr" />
    <h2>{{ pokemon.nameFr }}</h2>
    <div class="pokemon-types">
      <span
        v-for="t in pokemon.typesList"
        :key="t"
        class="pokemon-type"
        :class="t"
      >
        {{ t }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  props: ["pokemon"],
  mounted() {
    // Crée une liste de types directement sur le pokemon
    this.pokemon.typesList = this.pokemon.types?.map((t) => t.type.name) || [];
  },
  methods: {
    playCry() {
      if (!this.pokemon.cries?.latest) return;
      const audio = new Audio(this.pokemon.cries.latest);
      audio.play();
    },
  },
};
</script>

<style scoped>
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
  margin-top: 5px;
}

.pokemon-type {
  padding: 5px 10px;
  border-radius: 12px;
  color: white;
  font-size: 0.8em;
  text-transform: uppercase;
  font-weight: bold;
}

/* ===== Couleurs par type ===== */
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
