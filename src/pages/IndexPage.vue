<template>
  <q-page class="column flex-center">
    <q-knob
      v-model="count"
      :min="min"
      :max="max"
      size="80px"
      show-value
      :thickness="0.13"
      color="primary"
      track-color="dark"
    >
      <q-avatar size="75px">
        <img alt="Quasar logo" src="~assets/quasar-logo-inner.svg" />
      </q-avatar>
    </q-knob>

    <img
      alt="Quasar logo"
      src="~assets/quasar-logo-vertical.svg"
      style="width: 200px; height: 140px"
    />

    <div class="q-mt-xl">
      <q-btn
        color="primary"
        dense
        round
        label="-"
        :disable="count === min"
        @click="count--"
      />

      <span class="q-mx-md text-bold">{{ count }}</span>

      <q-btn
        color="primary"
        dense
        round
        label="+"
        :disable="count === max"
        @click="count++"
      />
    </div>

    <div class="q-mt-md" style="width: 200px">
      <q-slider v-model="count" :min="min" :max="max" />
    </div>
    <q-btn label="Prompt" color="primary" @click="prompt = true" />
    <q-input
      type="textarea"
      autogrow
      filled
      color="green"
      v-model="formattedSchedule"
    ></q-input>

    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">Your address</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input
            dense
            v-model="schedule"
            autofocus
            type="textfield"
            autogrow
            filled
            color="orange"
            @keyup.enter="prompt = false"
          />
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Clear" @click="schedule = ''" v-close-popup />
          <q-btn flat label="Add schedule" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script setup>
import { ref, reactive, computed } from 'vue';
// Import the functions you need from the SDKs you need
import { initializeApp } from 'firebase/app';
import { getFirestore, collection, addDoc } from 'firebase/firestore';
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
const firebaseConfig = {
  apiKey: 'AIzaSyCOIOu_S7a5KI91WRMQfUR0vbuF_qhB5Vw',
  authDomain: 'soundroomstatus.firebaseapp.com',
  databaseURL: 'https://soundroomstatus-default-rtdb.firebaseio.com',
  projectId: 'soundroomstatus',
  storageBucket: 'soundroomstatus.appspot.com',
  messagingSenderId: '494883848156',
  appId: '1:494883848156:web:abe8c6e6977074e2829919',
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

console.log(app);
const count = ref(0);
const schedule = ref('');

const formattedSchedule = computed(() => {
  if (schedule.value) {
    let str = schedule.value.toString();
    let separateLines = str.split(/\r?\n|\r|\n/g);
    return separateLines || '';
  } else {
    return '';
  }
});

const ft = ref(false);
const min = -5;
const max = 5;
const prompt = ref(false);
const FB = {
  add(provider) {
    addDoc(collection(db, 'users'), {
      name: provider,
      FT: ft,
    });
  },
};
</script>
