<template>
  <main>
    <div>
      <h1 class="font-bold text-4xl text-red-700 tracking-widest text-center mt-10">Welcome</h1>
      <br>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-x-6 gap-y-10">
      <div class="ml-10">
      </div>
      <div class="flex flex-col col-span-2">
        <table class="min-w-full shadow-md rounded">
          <thead class="bg-gray-50 text-xl">
            <tr>
              <th class="p-4 text-left">Event Name</th>
              <th class="p-4 text-left">Event Date</th>
              <th class="p-4 text-left">Attendees Amount</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-300">
            <tr v-for="event in queryData" :key="event._id">
              <td class="p-2 text-left">{{ event.eventName }}</td>
              <td class="p-2 text-left">{{ formattedDate(event.date) }}</td>
              <td class="p-2 text-left">{{ event.attendees }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    </div>
    <!--https://vue-chartjs.org/guide/#creating-your-first-chart-->

 <br>
    <Bar
    :chart-options="chartOptions"
    :chart-data="chartData"
    :chart-id="chartId"
    :dataset-id-key="datasetIdKey"
    :plugins="plugins"
    :css-classes="cssClasses"
    :styles="styles"
    :width="width"
    :height="height"
  />

  </main>
</template>
<script>

import { DateTime } from "luxon";
import axios from "axios";
import { Bar } from 'vue-chartjs';
import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

export default {
  name: 'BarChart',
  components: { Bar },
  props: {
    chartId: {
      type: String,
      default: 'bar-chart'
    },
    datasetIdKey: {
      type: String,
      default: 'label'
    },
    width: {
      type: Number,
      default: 150
    },
    height: {
      type: Number,
      default: 400
    },
    cssClasses: {
      default: '',
      type: String
    },
    styles: {
      type: Object,
      default: () => {}
    },
    plugins: {
      type: Object,
      default: () => {}
    }
  },
  data() {
    return {
      queryData: [],
      test:[],
      chartData: {
        labels: [ ],
        datasets: [ { data: [], backgroundColor:'#C8102E' } ]
      },
      chartOptions: {
        plugins: {
          legend: {
            display: false
          }
        },
        responsive: true,
        maintainAspectRatio:false,
        scales:{
          y:{
            ticks:{precision:0}
          }
        }
      }
    }
  },
  //gets data from recentEvent endpoint
  mounted() {
    let apiURL = import.meta.env.VITE_ROOT_API + `/eventdata/recentEvent/`;
    this.queryData = [];
    axios.get(apiURL).then((resp) => {
      this.queryData = resp.data;
      for (let i=0;i<this.queryData.length;i++){
        //appends event names to x axis
        this.chartData.labels.push(this.queryData[i].eventName);
        //appends attendee count to y axis
        this.test.push(this.queryData[i].attendees);
      }
      this.chartData.datasets[0].data=this.test;

    });
    window.scrollTo(0, 0);
  },
  methods: {
    routePush(routeName) {
      this.$router.push({ name: routeName });
    },
    formattedDate(datetimeDB) {
      return DateTime.fromISO(datetimeDB).plus({ days: 1 }).toLocaleString();
    },
  },
};
</script>
