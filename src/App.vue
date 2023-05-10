<template>
  <div class="q-pa-md col container" style="height: 250px">
    <div class="row-12">
      <q-input outlined v-model="distanceMinutes" bg-color="white" label="Input distance in minutes">
        <template v-slot:append>
          <q-avatar>
            <img src="https://cdn.quasar.dev/logo-v2/svg/logo.svg">
          </q-avatar>
        </template>
      </q-input>
      <q-select outlined v-model="pathChoice" :options="pathOptions" label="Choose  a path option" style="background: white;" />
    </div>
    <div class="row" style="padding-top: 15px">
      <q-btn color="primary" @click="getOpenRouterRequest" class="col-7" style="margin-right: 5px;">Run
        <q-tooltip>
          Run process
        </q-tooltip>
      </q-btn>
      <q-btn color="secondary" class="col-3" style="margin-right: 5px">Change</q-btn>
      <q-btn color="red" class="col" style="margin-right: 5px">Stop</q-btn>
    </div>

  </div>


</template>

<script>
import { defineComponent } from 'vue'
import { key } from 'assets/secured_data.js'
export default defineComponent({
  name: 'App',
  data() {
    return {
      pathChoice: '',
      pathOptions: ['Walk', 'Bicycle', 'Electric-bike', 'Car'],
      distanceMinutes: null,
      coordinates: [9.681495, 50.41461],
      pathMapping: {
        'Walk': 'foot-walking',
        'Bicycle': 'cycling-regular',
        'Electric-bike': 'cycling-electric',
        'Car': 'driving-car'
      }
    }
  },
  computed: {
    getDistanceSeconds() {
      return this.distanceMinutes * 60;
    },
    getPathKey() {
      return (this.pathChoice !== '') ? this.pathMapping[this.pathChoice] : 'driving-car';
    }
  },
  methods: {
    getOpenRouterRequest() {
      if (this.pathChoice === '') this.pathChoice = 'Car';
      console.log(this.getPathKey)
      let request = new XMLHttpRequest();

      request.open('POST', `https://api.openrouteservice.org/v2/isochrones/${this.getPathKey}`);

      request.setRequestHeader('Accept', 'application/json, application/geo+json, application/gpx+xml, img/png; charset=utf-8');
      request.setRequestHeader('Content-Type', 'application/json');
      request.setRequestHeader('Authorization', key);

      request.onreadystatechange = function () {
      if (this.readyState === 4) {
        console.log('Status:', this.status);
        console.log('Headers:', this.getAllResponseHeaders());
        console.log('Body:', this.responseText);
        }
      };
      const body = `{"locations":[[${this.coordinates[0]},${this.coordinates[1]}]],"range":[${this.getDistanceSeconds}]}`;
      request.send(body);
    }
  }
})
</script>

<style>
  .container {
    background-color: rgb(169, 175, 175);
    border-style: solid;
    border-width: 2px;
  }
</style>
