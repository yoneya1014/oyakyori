<template>
  <v-app>
    <v-container :style="style1">
      <div class="main">
        <p>現在の距離：{{ nowTotal }}m</p>
        <Chart />
      </div>
      <div class="button">
        <a href="/insert_data">
          <v-btn color="primary">記録を追加</v-btn>
        </a>
      </div>
    </v-container>
    <v-container :style="style2">
      <div class="main">
        <p>記録がありません</p>
      </div>
      <div class="button">
        <a href="/insert_data">
          <v-btn color="primary">記録を追加</v-btn>
        </a>
      </div>
    </v-container>
  </v-app>
</template>

<script>
import Chart from '../components/Chart'

export default {
  name: 'Index',
  components: { Chart },
  data() {
    return {
      nowTotal: null,
      style1: {
        display: 'none',
      },
      style2: {
        display: 'none',
      },
    }
  },
  mounted() {
    if (localStorage.getItem('nowtotal') !== null) {
      this.nowTotal = Number(localStorage.getItem('nowtotal'))
      this.style1.display = 'block'
    } else {
      this.style2.display = 'block'
    }
  },
}
</script>

<style scoped>
p {
  font-size: 1.5vw;
  text-align: center;
}
.main {
  width: 80%;
  margin: auto;
}
.button {
  margin: 30px 0;
  display: flex;
  width: 100%;
  justify-content: center;
}
canvas {
  margin: 0;
}
</style>
