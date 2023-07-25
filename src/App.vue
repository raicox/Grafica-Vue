<template>
  <main >
    <div class="grafica">
      <Line :data="filteredChartData" ref="line" :options="options" />
    </div>
    <div class="date-input">
      <input @input="filterData" type="date" class="startdate" v-model="startDate">
      <input @input="filterData" type="date" class="enddate" v-model="endDate">
    </div>
  </main>
</template>

<script>
import axios from 'axios';

import {
  Chart as ChartJS,
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
} from 'chart.js';
import { Line } from 'vue-chartjs';

ChartJS.register(
  CategoryScale,
  LinearScale,
  PointElement,
  LineElement,
  Title,
  Tooltip,
  Legend
);

export default {
  name: 'App',
  components: {
    Line
  },
  data() {
    return {
      startDate: '2023-07-23', 
      endDate: '2023-08-01', 
      dates: ['2023-07-23', '2023-07-24', '2023-07-25', '2023-07-26', '2023-07-27', '2023-07-28', '2023-07-29', '2023-07-30', '2023-07-31', '2023-08-01','2023-08-02','2023-08-03','2023-08-04','2023-08-05'],
      dataPoints: [1,4,5,7,5,10,9,8,9,10,11,12,13,14,15],
      chartData: {
        labels: this.dates,
        datasets: [
          {
            label: 'Información Api',
            backgroundColor: '#cfc7c766',
            borderColor: 'rgb(75, 192, 192)',
            data: this.dataPoints,
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false
      }
    };
  },
  mounted() {
    this.getDataFromAPI();
  },
  computed: {
    filteredChartData() {
      const startIndex = this.dates.indexOf(this.startDate);
      const endIndex = this.dates.indexOf(this.endDate);
      return {
        labels: this.dates.slice(startIndex, endIndex + 1),
        datasets: [
          {
            label: 'Data Points',
            data: this.dataPoints,
            borderColor: 'rgba(75, 192, 192, 1)',
            backgroundColor: 'rgba(75, 192, 192, 0.2)',
          }
        ]
      };
    }
  },
  methods: {
    filterData() {
      const startIndex = this.dates.indexOf(this.startDate);
      const endIndex = this.dates.indexOf(this.endDate);

      const filteredDates = this.dates.slice(startIndex, endIndex + 1);
      const filteredDataPoints = this.dataPoints.slice(startIndex, endIndex + 1);

      this.chartData.labels = filteredDates;
      this.chartData.datasets[0].data = filteredDataPoints;
    },

    getDataFromAPI() {
      const apiUrl = 'http://api.payfud.com/r/manganiello/v1/users';
      const token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNjkwMzE4MjcxLCJqdGkiOiI4Y2NiMTc4MGJjZjA0MjNkODA4ZTk5YTRjZjkzZDk2MSIsInVzZXJfaWQiOjEsInVzZXJuYW1lIjoibWFuZ2FuaWVsbG8iLCJpZCI6MSwicm9sIjoyfQ.-g7JYZ5Gl-yYFdT4FFB8ma24HafFuWh-sNV-7de9ztM"

      const config = {
        headers: {
          "Content-Type": "application-json",
          "Authorization": `Bearer ${token}`,
        },
        withCredentials: true,
      };

      axios
        .get(apiUrl, config)
        .then(response => {
          response.data.forEach(element => {
            this.chartData.labels.push(element.item)
          })
        })
        .catch(error => {
          console.error('Error al obtener los datos de la API:', error);
        });
    }
  }
}

</script>

<style>
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }

  main {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    height: 100vh;
    width: 100%;
    
  }

  .grafica {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 70%;
    min-width: 300px;
    height: 50vh;
    margin: 0 auto;
  }

  .date-input {
    display: flex;
    margin-top: 50px;
  }

  .date-input input {
    margin: 0 20px;
  }
</style>