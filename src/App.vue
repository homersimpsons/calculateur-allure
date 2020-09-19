<template>

<div class="container">
  <h1 class="text-center">Calculateur d'allure</h1>
  <div class="row">
    <div class="col-6">
      <div class="input-group mb-2">
        <div class="input-group-prepend">
          <div class="input-group-text">VMA</div>
        </div>
        <input class="form-control" v-model.number="vma" min="3" max="26" type="number" step="0.05" />
      </div>
    </div>
    <div class="col-6">
      <div class="input-group mb-2">
        <div class="input-group-prepend">
          <div class="input-group-text">IE</div>
        </div>
        <input class="form-control" v-model.number="ie" min="-12" max="-3" type="number" step="0.05" />
      </div>
    </div>
  </div>

  <table class="table table-stripped">
    <thead>
      <tr>
        <th>Allures basées sur la durée</th>
        <th>Durée (s)</th>
        <th>Objectif (km)</th>
        <th>Allure (km/h)</th>
        <th>% VMA</th>
        <th>% FCM</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="duration in durations" :key="duration.duration">
        <th>{{ duration.label }}</th>
        <td>{{ duration.duration }}</td>
        <td>{{ (speed(duration.duration) * duration.duration / 3600).toFixed(3) }}</td>
        <td>{{ speed(duration.duration).toFixed(2) }}</td>
        <td>{{ (speed(duration.duration) * 100/ vma).toFixed(1) }} %</td>
        <td>
          <template v-if="duration.fcm">{{ (duration.fcm * 100).toFixed(1) }} %</template>
          <template v-else>-</template>
        </td>
      </tr>
    </tbody>
  </table>
</div>
</template>

<script>
import {ref, computed} from 'vue';

export default {
  name: 'App',
  setup() {
    const vma = ref(17);
    const ie = ref(-6.5);

    const speed = computed(() => {
      return (duration) => {
        if (duration < 6*60) {
          return 0; // @TODO
        }
        if (duration < 3.5*3600) {
          return vma.value + (vma.value / 100) * ie.value * Math.log(duration / 360);
        }
        // @TODO This is not exactly the formula used
        return vma.value + (vma.value / 100) * ie.value * Math.log(3.5*3600 / 360) + (vma.value / 50) * ie.value * Math.log(duration / (3.5*3600));
      }
    })

    const durations = [
      {duration: 7, label: 'Vitesse maximale (sprint)', fcm: null},
      {duration: 6*60, label: 'Vitesse maximale arobie (T = 6\')', fcm: 1},
      {duration: 15*60, label: '15 minutes', fcm: 0.969},
      {duration: 30*60, label: 'Seuil+ (T = 30\')', fcm: 0.934},
      {duration: 1*3600, label: 'Seul lactique (T = 1h)', fcm: 0.9},
      {duration: 2*3600, label: '2 heures', fcm: 0.866},
      {duration: 3*3600, label: '3 heures', fcm: 0.845},
      {duration: 4*3600, label: '4 heures', fcm: 0.816},
      {duration: 4.5*3600, label: 'Seuil aérobie (T = 4,5h)', fcm: 0.8},
      {duration: 5*3600, label: '5 heures', fcm: 0.787},
      {duration: 6*3600, label: '6 heures', fcm: 0.763},
      {duration: 7*3600, label: '7 heures', fcm: 0.741},
      {duration: 8*3600, label: '8 heures', fcm: 0.72},
      {duration: 9*3600, label: '9 heures', fcm: 0.712},
      {duration: 12*3600, label: '12 heures', fcm: 0.689},
      {duration: 15*3600, label: '15 heures', fcm: 0.669},
      {duration: 24*3600, label: '24 heures', fcm: 0.62},
    ];

    return {
      vma,
      ie,
      speed,
      durations,
    };
  },
};
</script>
