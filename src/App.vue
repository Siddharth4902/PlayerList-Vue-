<template>
  <div id="app">
    <h1>Sportz Interactive</h1>

  
    <div class="filters">
      <label for="player-name-filter">Filter by Player Name:</label>
      <input 
        type="text" 
        id="player-name-filter" 
        v-model="playerNameFilter" 
        placeholder="Enter player name"
      />
      
      <label for="team-name-filter">Filter by Country:</label>
      <select 
        id="team-name-filter" 
        v-model="teamNameFilter"
      >
        <option value="">All Countries</option>
        <option v-for="country in uniqueCountries" :key="country" :value="country">
          {{ country }}
        </option>
      </select>
    </div>
    <div class="columns">
      <div class="column">
        <h2>Batsmen</h2>
        <div v-for="player in filteredPlayers.batsmen" :key="player.name" class="player-card">
          <img :src="player.image" alt="Player Image" v-if="player.image"/>
          <div class="player-info">
            <h3>{{ player.name }}</h3>
            <p>Matches: {{ player.matches }}</p>
            <p>Runs: {{ player.runs }}</p>
            <p>50s: {{ player["50s"] }}</p>
            <p>100s: {{ player["100s"] }}</p>
            <p>Highest Score: {{ player.highest_score }}</p>
            <p>Best Bowling Figures: {{ player.best_bowling_figures }}</p>
            <p>Team: {{ player.team_name }}</p>
          </div>
        </div>
      </div>
      
      <div class="column">
        <h2>All-Rounders</h2>
        <div v-for="player in filteredPlayers.allRounders" :key="player.name" class="player-card">
          <img :src="player.image" alt="Player Image" v-if="player.image"/>
          <div class="player-info">
            <h3>{{ player.name }}</h3>
            <p>Matches: {{ player.matches }}</p>
            <p>Runs: {{ player.runs }}</p>
            <p>50s: {{ player["50s"] }}</p>
            <p>100s: {{ player["100s"] }}</p>
            <p>Highest Score: {{ player.highest_score }}</p>
            <p>Best Bowling Figures: {{ player.best_bowling_figures }}</p>
            <p>Team: {{ player.team_name }}</p>
          </div>
        </div>
      </div>

      <div class="column">
        <h2>Bowlers</h2>
        <div v-for="player in filteredPlayers.bowlers" :key="player.name" class="player-card">
          <img :src="player.image" alt="Player Image" v-if="player.image"/>
          <div class="player-info">
            <h3>{{ player.name }}</h3>
            <p>Matches: {{ player.matches }}</p>
            <p>Runs: {{ player.runs }}</p>
            <p>50s: {{ player["50s"] }}</p>
            <p>100s: {{ player["100s"] }}</p>
            <p>Highest Score: {{ player.highest_score }}</p>
            <p>Best Bowling Figures: {{ player.best_bowling_figures }}</p>
            <p>Team: {{ player.team_name }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      players: [],
      playerNameFilter: '',
      teamNameFilter: ''
    };
  },
  methods: {
    fetchPlayers() {
      axios.get('../players.json') // Update this URL to the actual path of your JSON file
        .then(response => {
          this.players = response.data.originalPlayers;
        })
        .catch(error => {
          console.error('There was an error fetching the data:', error);
        });
    },
    getRole(roleId) {
      const roles = ['Batsman', 'All-Rounder', 'Bowler'];
      return roles[roleId - 2] || 'Unknown';
    }
  },
  computed: {
    filteredPlayers() {
      const playersByRole = {
        batsmen: [],
        allRounders: [],
        bowlers: []
      };

      this.players.forEach(player => {
        const role = this.getRole(player.role);
        if (role === 'Batsman') {
          playersByRole.batsmen.push(player);
        } else if (role === 'All-Rounder') {
          playersByRole.allRounders.push(player);
        } else if (role === 'Bowler') {
          playersByRole.bowlers.push(player);
        }
      });

      const filterByNameAndTeam = (playerList) => {
        return playerList.filter(player => {
          const nameMatch = player.name.toLowerCase().includes(this.playerNameFilter.toLowerCase());
          const countryMatch = this.teamNameFilter ? player.team_name === this.teamNameFilter : true;
          return nameMatch && countryMatch;
        });
      };

      return {
        batsmen: filterByNameAndTeam(playersByRole.batsmen),
        allRounders: filterByNameAndTeam(playersByRole.allRounders),
        bowlers: filterByNameAndTeam(playersByRole.bowlers)
      };
    },
    uniqueCountries() {
      return [...new Set(this.players.map(player => player.team_name))];
    }
  },
  created() {
    this.fetchPlayers();
  }
};
</script>

<style scoped>
#app {
  text-align: center;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  margin-top: 60px;
}
.filters {
  margin-bottom: 20px;
}
.filters label {
  margin-right: 10px;
}
.filters input, .filters select {
  margin-right: 20px;
}
.columns {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
}
.column {
  flex: 1;
  margin: 10px;
  min-width: 250px;
}
.player-card {
  display: flex;
  align-items: center;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin: 10px 0;
  padding: 10px;
  background: #f9f9f9;
}
.player-info {
  margin-left: 15px;
}
.player-info img {
  max-width: 150px;
  border-radius: 8px;
}
</style>
