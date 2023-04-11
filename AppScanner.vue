<template>
    <div class="container mx-auto px-4 mt-20">
      <h1>Get Now:</h1>
      <!-- <h6 v-for="(event, index) in eventsData" :key="index">{{ event.eventid }}</h6> -->
      <!-- <h6>{{ eventsData }}</h6> -->
  
      <container class="table-container">
        <table class="table-auto w-full">
        <thead>
            <tr class="bg-gray-200 text-gray-600 text-sm font-semibold tracking-wide uppercase">
            <th class="px-4 py-3">Event ID</th>
            <th class="px-4 py-3">Date</th>
            <th class="px-4 py-3">League</th>
            <th class="px-4 py-3">Home Team</th>
            <th class="px-4 py-3">Away Team</th>
            <th class="px-4 py-3">Score</th>
            <th class="px-4 py-3">GT</th>
            <th class="px-4 py-3">TT</th>
            <th class="px-4 py-3">Over Spread</th>
            <th class="px-4 py-3">Under Spread</th>
            <th class="px-4 py-3">Home Odds</th>
            <th class="px-4 py-3">Draw Odds</th>
            <th class="px-4 py-3">Away Odds</th>
            </tr>
        </thead>
        <tbody class="text-gray-600 text-sm font-light">
            <tr v-for="(event, index) in eventsData" :key="index" class="border-b border-gray-200 hover:bg-gray-100">
            <td class="px-4 py-3">{{ event.eventid }}</td>
            <td class="px-4 py-3">{{ event.date }}</td>
            <td class="px-4 py-3">{{ event.league }}</td>
            <td class="px-4 py-3">{{ event.home_name }}</td>
            <td class="px-4 py-3">{{ event.away_name }}</td>
            <td class="px-4 py-3">{{ event.ss }}</td>
            <td class="px-4 py-3">{{ event.GT }}</td>
            <td class="px-4 py-3">{{ event.TT }}</td>
            <td class="px-4 py-3">{{ event.over_spread }}</td>
            <td class="px-4 py-3">{{ event.under_spread }}</td>
            <td class="px-4 py-3">{{ event.oddsKO.home_od }}</td>
            <td class="px-4 py-3">{{ event.oddsKO.draw_od }}</td>
            <td class="px-4 py-3">{{ event.oddsKO.away_od }}</td>
            </tr>
        </tbody>
    </table>
    </container>
  
  
  
      <h1>GraphLive:</h1>
      <h6>{{ eventDataId }}</h6>
  
      <br>
  
      <h1>LiveGames:</h1>
      <h6> </h6>
  
      <br>
  
      <h1>Odds:</h1>
      <h6> ... </h6>
    </div>
  </template>
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        messageGraphLive: '',
        messageId: '',
        messageOdds: '',
        eventSource: null,
  
        eventData: [],
        eventDataId: [],
        fullData: [],
  
        allData: [],
        eventsData: [],
        eventMap: {},
      };
    },
    mounted() {
      // Messaggi GraphLive
      this.eventSourceGraphLive = new EventSource('http://localhost:8000/events/graphlive');
      this.eventSourceGraphLive.onmessage = (event) => {
        const data = JSON.parse(event.data);
        const index = this.eventDataId.indexOf(data.eventid);
  
        if (index == -1) {
          this.eventDataId.push(data.eventid);
          this.eventData.push(data);
        } else {
          this.eventData.splice(index, 1);
          this.eventData.push(data);
        }
      };
  
      // Messaggi ID
      // this.eventSourceId = new EventSource('http://localhost:8000/events/id');
      // this.eventSourceId.onmessage = (event) => {
      //   this.messageId = event.data;
      // }
  
      //Messaggi Odds
    //   this.eventSourceOdds = new EventSource('http://localhost:8000/events/odds');
    //   this.eventSourceOdds.onmessage = (event) => {
    //     this.messageOdds = event.data;
    //   };
  
      setInterval(() => {
        this.fecthAllData();
      }, 15000);
    },
    // async mounted() {
    //   this.allData = await this.fecthAllData();
    // },
    beforeDestroy() {
      // Chiusura della connessione Server-Sent Events quando il componente viene distrutto
      this.eventSource.close();
    },
    methods: {
     async fecthAllData() {
      const response = await axios.get('http://localhost:8000/events/get_data_now');
      this.allData = JSON.parse(response.data);   // Contiene tutte le partite
  
      for (let i=0; i < this.allData.length; i++) {
        const event = this.allData[i];
  
        if (!this.eventMap[event.eventid]) {
          this.eventsData.push(event);
          this.eventMap[event.eventid] = true;
          console.log("Partita aggiunta (" + event.eventid + ")");
        } else {
          console.log("PARTITA GIA PRESENTE");
        }
      }
      
      console.log(this.eventMap)
     }
    },
  };
  </script>

<style>
.table-container {
    margin-top: 300px;
}
</style>