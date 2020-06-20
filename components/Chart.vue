<template>
  <canvas id="chart"></canvas>
</template>

<script>
import * as firebase from 'firebase/app'
import 'firebase/database'

export default {
  name: 'Chart',
  data() {
    return {
      chartData: {},
    }
  },
  mounted() {
    if (!firebase.apps.length) {
      firebase.initializeApp({
        apiKey: 'AIzaSyBC2MtUpQFAkMlRE4BAMwHK6X9GHZ6hBEw',
        authDomain: 'oya-kyori.firebaseapp.com',
        databaseURL: 'https://oya-kyori.firebaseio.com',
        projectId: 'oya-kyori',
        storageBucket: 'oya-kyori.appspot.com',
        messagingSenderId: '3768367436',
        appId: '1:3768367436:web:c5659a0baaa6e5b6db77e9',
      })
    }
    const database = firebase.database()
    database
      .ref('data')
      .once('value')
      .then((snapshot) => {
        const data = snapshot.val()
        const res = {
          type: 'line',
          data: {
            labels: [],
            datasets: [
              {
                label: '今までの記録',
                data: [],
                borderColor: '#7fffd4',
                fill: false,
                lineTension: 0.3,
              },
            ],
          },
          options: {
            scales: {
              yAxes: [
                {
                  ticks: {
                    min: 0,
                    stepSize: 1000,
                  },
                },
              ],
            },
          },
        }
        for (let i = 0; i < data.length; i++) {
          res.data.labels.push(data[i].date)
          res.data.datasets[0].data.push(data[i].updateTotal)
        }
        this.chartData = res
      })
      .catch((err) => {
        console.log(err)
      })
      .finally(() => {
        const canvas = document.getElementById('chart').getContext('2d')
        // eslint-disable-next-line no-undef,no-new
        new Chart(canvas, this.chartData)
      })
  },
  head: {
    script: [
      {
        src:
          'https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.1/Chart.min.js',
      },
    ],
  },
}
</script>

<style scoped>
#chart {
  width: 80vw;
  height: auto;
}
</style>
