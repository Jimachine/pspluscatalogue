<script setup>
import { ref, onMounted, computed } from "vue";
import Ps5Icon from "@/assets/ps5.svg";
import Ps4Icon from "@/assets/ps4.svg";

const iconMap = {
  PS5: Ps5Icon,
  PS4: Ps4Icon,
};

const games = ref([]);
const search = ref("");
const platformFilter = ref("");

// Fetch data on mount
onMounted(async () => {
  const res = await fetch(
    "/psapi/bin/imagic/gameslist?locale=en-gb&categoryList=plus-games-list"
  );
  const data = await res.json();
  const allGames = data.flatMap((group) => group.games);
  games.value = allGames;
});

// Filtering logic
const filteredGames = computed(() => {
  return games.value.filter((game) => {
    const matchesSearch = game.name
      .toLowerCase()
      .includes(search.value.toLowerCase());
    const matchesPlatform = platformFilter.value
      ? game.platform === platformFilter.value
      : true;
    return matchesSearch && matchesPlatform;
  });
});
</script>

<template>
  <div class="app">
    <h1>PlayStation Plus Catalogue</h1>

    <!-- <input v-model="search" placeholder="Search games..." />

    <select v-model="platformFilter">
      <option value="">All Platforms</option>
      <option value="PS5">PS5</option>
      <option value="PS4">PS4</option>
    </select> -->

    <div class="games">
      <div v-for="game in games" :key="game.conceptId" class="game-card">
        <img :src="game.imageUrl" :alt="game.name" />
        <p class="game-title">{{ game.name }}</p>
        <ul class="device-list">
          <li v-for="device in game.device" :key="device">
            <img :src="iconMap[device]" :alt="device" class="device-icon" />
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<style scoped>
.games {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
}

.game-card {
  text-align: center;
  border: 1px solid #ddd;
  border-radius: 10px;
  padding: 12px;
  background: #ffffff0d;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.08);
}

img {
  width: 100%;
  border-radius: 8px;
}

.game-title {
  min-height: 75px;       /* increase height for 2–3 lines */
  overflow: hidden;        /* prevents super-long text from pushing layout */
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 0 6px;
  font-size: 0.95rem;
  line-height: 1.2;
}


.device-list {
  display: flex;
  justify-content: center;   /* center the whole row */
  align-items: center;
  gap: 10px;                 /* spacing between icons */
  margin: 10px 0 0;
  padding: 0;
  list-style: none;
}

.device-list li {
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.12);
  padding: 6px 10px;
  border-radius: 8px;
}

.device-icon {
  width: 36px;   /* much bigger */
  height: 36px;
  filter: brightness(0) invert(1);
  display: block;
}

</style>
