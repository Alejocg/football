<template>
  <div class="flex flex-col min-h-screen bg-gradient-to-b from-purple-900 via-red-900 to-blue-900 text-gray-100">
    <header class="bg-gradient-to-r from-gray-900 to-purple-900 py-3 px-6 sticky top-0 z-50">
      <h1 class="text-3xl font-bold">What match to watch next</h1>
    </header>
    <main class="flex-1 p-4">
      <div class="max-w-xl mx-auto">
        <h2 class="text-2xl font-bold text-white mb-6 bg-gradient-to-r from-gray-900 to-purple-900 py-2 px-4 shadow-md rounded-md">Next Matches for {{ selectedTeam }}</h2>
        <div class="mb-4">
          <label for="team-select" class="font-bold text-white text-lg mr-2">Select a team:</label>
          <select id="team-select" v-model="selectedTeam" @change="changeTeam" class="border bg-yellow-600 border-gray-400 rounded-md px-2 py-1">
            <option v-for="team in teams" :key="team.id" :value="team.name">{{ team.name }}</option>
          </select>
        </div>
        <div v-if="loading" class="text-2xl font-bold mb-4">Loading...</div>
        <div v-else class="text-lg">
          <div v-if="matches.length > 0" class="mb-4">
            <h3 class="text-xl font-bold text-white mb-2">Next Match</h3>
            <div class="bg-gradient-to-r from-red-800 to-gray-900 transition duration-300 ease-in-out rounded-md border border-gray-400 p-4 over:from-white hover:to-black">
              <div class="mb-2">
                <span class="font-bold">Date:</span> {{ formatDate(matches[0].fixture.date) }}
              </div>
              <div class="mb-2">
                <span class="font-bold">Home team:</span> {{ matches[0].teams.home.name }}
              </div>
              <div class="mb-2">
                <span class="font-bold">Away team:</span> {{ matches[0].teams.away.name }}
              </div>
              <div>
                <span class="font-bold">Venue:</span> {{ matches[0].fixture.venue.name }}
              </div>
            </div>
          </div>
          <div v-if="matches.length > 1">
            <h3 class="text-xl font-bold text-white mb-2">Next Match After That</h3>
            <div class="bg-gradient-to-r from-red-800 to-gray-900 hover:shadow-md transition duration-300 ease-in-out rounded-md border border-gray-400 p-4 over:from-white hover:to-black">
              <div class="mb-2">
                <span class="font-bold">Date:</span> {{ formatDate(matches[1].fixture.date) }}
              </div>
              <div class="mb-2">
                <span class="font-bold">Home team:</span> {{ matches[1].teams.home.name }}
              </div>
              <div class="mb-2">
                <span class="font-bold">Away team:</span> {{ matches[1].teams.away.name }}
              </div>
              <div>
                <span class="font-bold">Venue:</span> {{ matches[1].fixture.venue.name }}
              </div>
            </div>
          </div>
          <div v-else>No upcoming match found.</div>
        </div>
      </div>
    </main>
    <footer class="bg-gradient-to-r from-gray-900 to-purple-900 text-gray-300 py-3 px-6 flex items-center justify-between sticky bottom-0 z-50">
      <div>Made by Alejo with <i class="fas fa-heart text-red-500"></i></div>
      <a href="https://github.com/alejocg/football" target="_blank" rel="noopener noreferrer">
        <i class="fab fa-github text-white hover:text-gray-300"></i>
      </a>
    </footer>
  </div>
</template>



<script>
export default {
  data() {
    return {
      loading: true,
      matches: [],
      apiKey: "2bb57441c3ef9dd462e504182561954a",
      teamId: 529, // Barcelona's team ID in the API
      teams: [
        {
          name: "Barcelona",
          id: 529,
        },
        {
          name: "Real Madrid",
          id: 541,
        },
        {
          name: "Manchester City",
          id: 50,
        },
        {
          name: "Fribourg",
          id: 160,
        },
      ],
      selectedTeam: "Barcelona",
    };
  },
  methods: {
    async fetchNextMatches() {
      const response = await fetch(
        `https://v3.football.api-sports.io/fixtures?team=${this.teamId}&next=2`,
        {
          headers: {
            "x-apisports-key": this.apiKey,
          },
        }
      );
      const data = await response.json();
      this.matches = data.response;
      this.loading = false;
    },
    formatDate(date) {
      const options = {
        year: "numeric",
        month: "long",
        day: "numeric",
        hour: "numeric",
        minute: "numeric",
      };
      return new Date(date).toLocaleDateString("en-US", options);
    },
    async changeTeam() {
      const team = this.teams.find((t) => t.name === this.selectedTeam);
      this.teamId = team.id;
      this.loading = true;
      await this.fetchNextMatches();
    },
  },
  async mounted() {
    await this.fetchNextMatches();
  },
  watch: {
    selectedTeam: "changeTeam",
  },
};
</script>
