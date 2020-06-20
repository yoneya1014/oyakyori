<template>
  <v-app>
    <v-container>
      <v-row>
        <v-col>
          <v-dialog
            ref="dialog"
            v-model="modal"
            :return-value.sync="date"
            persistent
            width="300px"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-text-field
                v-model="date"
                label="今日の日付"
                readonly
                v-bind="attrs"
                v-on="on"
              ></v-text-field>
            </template>
            <v-date-picker v-model="date" scrollable>
              <v-spacer></v-spacer>
              <v-btn text color="primary" @click="modal = false"
                >キャンセル
              </v-btn>
              <v-btn text color="primary" @click="$refs.dialog.save(date)"
                >完了
              </v-btn>
            </v-date-picker>
          </v-dialog>
        </v-col>
      </v-row>
      <v-card flat color="transparent">
        <v-subheader>今日はうまく話せた？</v-subheader>
        <v-slider v-model="values.data1" step="10" ticks="always" tick-size="4">
        </v-slider>
        <v-row>
          <v-col cols="6">
            <p>できなかった</p>
          </v-col>
          <v-col cols="6">
            <p style="text-align: right;">できた</p>
          </v-col>
        </v-row>
      </v-card>
      <v-card flat color="transparent">
        <v-subheader>イライラしなかった？</v-subheader>
        <v-slider v-model="values.data2" step="10" ticks="always" tick-size="4">
        </v-slider>
        <v-row>
          <v-col cols="6">
            <p>しなかった</p>
          </v-col>
          <v-col cols="6">
            <p style="text-align: right;">した</p>
          </v-col>
        </v-row>
      </v-card>
      <v-card flat color="transparent">
        <v-subheader>しっかり手伝いはできた？</v-subheader>
        <v-slider v-model="values.data3" step="10" ticks="always" tick-size="4">
        </v-slider>
        <v-row>
          <v-col cols="6">
            <p>できなかった</p>
          </v-col>
          <v-col cols="6">
            <p style="text-align: right;">できた</p>
          </v-col>
        </v-row>
      </v-card>
      <v-card flat color="transparent">
        <v-subheader>明日は仲良くできそう？</v-subheader>
        <v-slider v-model="values.data4" step="10" ticks="always" tick-size="4">
        </v-slider>
        <v-row>
          <v-col cols="6">
            <p>できなさそう</p>
          </v-col>
          <v-col cols="6">
            <p style="text-align: right;">できそう</p>
          </v-col>
        </v-row>
      </v-card>
      <v-row justify="end">
        <v-btn color="primary" @click="resetConf">
          構成リセット
        </v-btn>
        <v-btn color="primary" @click="insertData">
          保存する
        </v-btn>
        <div id="space" />
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
import * as firebase from 'firebase/app'
import 'firebase/database'

export default {
  name: 'InsertData',
  data() {
    return {
      modal: false,
      date: new Date().toISOString().substr(0, 10),
      values: {
        data1: 0,
        data2: 0,
        data3: 0,
        data4: 0,
      },
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
  },
  methods: {
    insertData() {
      const database = firebase.database()
      const updateValue =
        this.values.data1 +
        this.values.data2 +
        this.values.data3 +
        this.values.data4 -
        200
      let nowtotal = 0
      if (localStorage.getItem('nowtotal') === null) {
        nowtotal = 5000
      } else {
        nowtotal = Number(localStorage.getItem('nowtotal'))
      }
      nowtotal = nowtotal - updateValue
      localStorage.setItem('nowtotal', String(nowtotal))
      database
        .ref('data')
        .once('value')
        .then((snapshot) => {
          const data = snapshot.val()
          if (data === null) {
            database.ref('data').set([
              {
                date: this.date,
                value: this.values,
                updateTotal: nowtotal,
              },
            ])
          } else {
            let duplicationFrag = false
            for (let i = 0; i < data.length; i++) {
              if (data[i].date === this.date) {
                data[i] = {
                  date: this.date,
                  value: this.values,
                  updateTotal: nowtotal,
                }
                duplicationFrag = true
                break
              }
            }
            if (!duplicationFrag) {
              data.push({
                date: this.date,
                value: this.values,
                updateTotal: nowtotal,
              })
            }
            database.ref('data').update(data)
          }
        })
        .catch((err) => {
          console.log(err)
        })
        .finally(() => {
          location.href = '/'
        })
    },
    resetConf() {
      localStorage.clear()
      location.href = '/'
    },
  },
}
</script>

<style scoped>
p {
  font-size: 1em;
}
#space {
  display: block;
  width: 25px;
}
</style>
