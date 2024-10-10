<template>
  <div>
    <h1>Player List</h1>
    <input type="text" v-model="searchQuery" placeholder="Search..." />
    <button @click="clearSearch">X</button>

    <ul class="no-bullets">
      <li v-for="(player, index) in filteredPlayers" :key="index">
        <h2>
          U-id: {{ index + 1 }} || 
          Player Name: {{ player.name || '' }} || 
          Player Address: {{ player.address || '' }} || 
          Player Gender: {{ player.gender || '' }} || 
          Date of Birth: {{ player.dob || '' }} || 
          Player Email: {{ player.email || '' }} || 
          Player Phone No: {{ player.tel || '' }}
          <button @click="removePlayer(index)">Remove</button>
          <button @click="editPlayer(index)">Edit</button>
          <button @click="duplicatePlayer(index)">Duplicate</button>
        </h2>
      </li>
    </ul>

    <div v-if="isEditing">
      <h2>Edit Player</h2>
      <input v-model="currentPlayer.name" placeholder="Name">
      <input v-model="currentPlayer.address" placeholder="Address">
      <input v-model="currentPlayer.gender" placeholder="Gender">
      <input v-model="currentPlayer.dob" placeholder="Date Of Birth">
      <input v-model="currentPlayer.email" placeholder="Email">
      <input v-model="currentPlayer.tel" placeholder="Phone No.">
      <button @click="saveEdit">Save</button>
      <button @click="cancelEdit">Cancel</button>
    </div>

    <ul class="no-bullets">
      <li v-for="(player, index) in paginatedItems" :key="index">
        <!-- {{ player.name }} -->
      </li>
    </ul>

    <div>
      <button type="submit" @click="previousPage" :disabled="currentPage === 1">Previous</button>
      <span> Page {{ currentPage }} of {{ totalPages }} </span>
      <button type="submit" @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
  </div>
</template>

<script setup>
import useStore from '../store/Store';
import { ref, computed } from 'vue';

const store = useStore(); // Access the shared store
const searchQuery = ref('');
const isEditing = ref(false);
const currentPlayerIndex = ref(null);
const currentPlayer = ref({});

// Clear search query
const clearSearch = () => {
  searchQuery.value = '';
};

// Remove player
const removePlayer = (index) => {
  if (confirm("Are you sure to remove your details!")) {
    store.players.splice(index, 1);
  }
};

// Edit player
const editPlayer = (index) => {
  isEditing.value = true;
  currentPlayer.value = { ...store.players[index] };
  currentPlayerIndex.value = index;
};

// Save edited player
const saveEdit = () => {
  if (currentPlayerIndex.value !== null) {
    store.players[currentPlayerIndex.value] = currentPlayer.value;
  }
  isEditing.value = false;
  currentPlayerIndex.value = null;
};

// Cancel edit
const cancelEdit = () => {
  isEditing.value = false;
};

// Duplicate player
const duplicatePlayer = (index) => {
  if (confirm("Are you sure, you want to duplicate this player?")) {
    const playerToDuplicate = store.players[index];
    const duplicatedPlayer = { ...playerToDuplicate };
    duplicatedPlayer.name = `${duplicatedPlayer.name} (Copy)`;
    store.players.push(duplicatedPlayer);
  }
};

// Filter players based on search query
const filteredPlayers = computed(() => {
  if (!searchQuery.value) {
    return store.players; // Return all players if no search query
  }
  
  const query = searchQuery.value.toLowerCase();
  return store.players.filter(player => {
    return (
      player.name.toLowerCase().includes(query) ||
      player.address.toLowerCase().includes(query) ||
      player.gender.toLowerCase().includes(query) ||
      player.dob.includes(query) ||
      player.email.toLowerCase().includes(query) ||
      player.tel.includes(query)
    );
  });
});

const itemsPerPage = 5;
const currentPage = ref(1);

const totalPages = computed(() => Math.ceil(filteredPlayers.value.length / itemsPerPage));

const paginatedItems = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return filteredPlayers.value.slice(start, start + itemsPerPage);
});

// Pagination methods
const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

const previousPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};
</script>

<style scoped>
h1, h2 {
  font-family: "Coming Soon", cursive;
  font-weight: 400;
  font-style: normal;
}

input {
  padding: 0.5rem;
  margin-bottom: 0.7rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-family: "Coming Soon", cursive;
}

button {
  background-color: #81bcfa;
  color: white;
  padding: 0.75rem 1.5rem;
  margin-right: 4px;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s ease-in-out;
  margin-top: 20px;
  font-family: "Coming Soon", cursive;
}

button:hover {
  background-color: #0069d9;
}

.no-bullets{
  list-style-type: none;
  font-size: medium;
}

</style>
