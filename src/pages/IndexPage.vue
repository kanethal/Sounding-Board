<template>
  <q-page class="column flex-center">
    <q-btn label="Prompt" color="primary" @click="prompt = true" />
    <div class="q-pa-md">
      <q-table
        title="Schedule"
        :rows="formattedSchedule"
        :columns="columns"
        row-key="provider"
      />
    </div>
    {{ formattedSchedule }}
    <q-dialog v-model="prompt" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">Paste in copied schedule from Qgenda</div>
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
    let arr = str.split(/\r?\t?\n|\r|\n/g);
    let pairs = [];
    for (let i = 0; i < arr.length + -1; i += 2) {
      let row = {
        provider: arr[i],
        shift: arr[i + 1],
      };
      pairs.push(row);
    }
    return pairs;
  }
  return [{ provider: 'dolly pardon', shift: '9 to 5' }];
});

const ft = ref(false);
const min = -5;
const max = 5;
const prompt = ref(false);
</script>
