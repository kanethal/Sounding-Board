<template>
  <q-page class="row fullscreen">
    <!-- container -->
    <div class="column col-12 q-pa-sm">
      <!-- card container -->
      <q-card class="col-12 column">
        <!-- Make it tabs -->
        <q-tab-panels class="col column" v-model="tab" animated>
          <!-- Import tab -->
          <q-tab-panel class="fit column" name="submit">
            <q-scroll-area class="col">
              <q-table
                title="Import Schedule"
                dense
                class=""
                :rows="formattedSchedule"
                :pagination="pagination"
                :columns="columns"
                row-key="provider"
              >
                <template v-slot:body="props">
                  <q-tr :props="props">
                    <q-td key="provider" :props="props">
                      {{ props.row.provider }}
                      <q-popup-edit
                        v-model="props.row.provider"
                        title="Edit Provider"
                        auto-save
                        v-slot="scope"
                      >
                        <q-input
                          v-model="scope.value"
                          dense
                          autofocus
                          counter
                          @keyup.enter="scope.set"
                        />
                      </q-popup-edit>
                    </q-td>
                    <q-td key="shift" :props="props">
                      {{ props.row.shift }}
                      <q-popup-edit
                        v-model="props.row.shift"
                        title="Edit Shift"
                        auto-save
                        v-slot="scope"
                      >
                        <q-input
                          v-model="scope.value"
                          dense
                          autofocus
                          counter
                          @keyup.enter="scope.set"
                        />
                      </q-popup-edit>
                    </q-td>
                  </q-tr>
                </template>
              </q-table>
            </q-scroll-area>
            <div class="row full-width">
              <q-btn
                class="col"
                color="red"
                label="Clear"
                flat
                v-if="schedule"
                @click="
                  () => {
                    schedule = '';
                    scheduleDate = '';
                    formattedSchedule = [
                      { provider: 'dolly pardon', shift: '9 to 5' },
                    ];
                  }
                "
              />
              <q-btn
                v-if="!schedule"
                label="Add Schedule"
                class="col"
                color="primary"
                @click="prompt = true"
              />
              <q-btn
                v-if="schedule"
                class="col"
                label="Next"
                color="green"
                @click="pushSchedule"
              />
            </div>
            <q-dialog v-model="prompt" persistent>
              <q-card style="min-width: 350px">
                <q-expansion-item
                  group="import"
                  default-opened
                  expand-separator
                  header-class="text-primary"
                  icon="event"
                  label="Schedule Date"
                  :caption="scheduleDate"
                >
                  <q-card-section>
                    <div class="text-h6">Select the schedule date</div>
                  </q-card-section>
                  <q-card-section>
                    <q-date v-model="scheduleDate" landscape />
                  </q-card-section>
                </q-expansion-item>
                <q-expansion-item
                  header-class="text-teal"
                  expand-separator
                  :model-value="!!scheduleDate"
                  group="import"
                  icon="perm_identity"
                  label="Schedule import"
                >
                  <q-card-section class="q-pt-none">
                    <div class="text-h6">
                      Paste in copied schedule from Qgenda
                    </div>

                    <q-scroll-area style="height: 400px">
                      <q-input
                        dense
                        v-model="schedule"
                        autofocus
                        type="textfield"
                        autogrow
                        filled
                        color="orange"
                        @keyup.enter="formatSchedule"
                      />
                    </q-scroll-area>
                  </q-card-section>
                </q-expansion-item>

                <q-card-actions align="right" class="text-primary">
                  <q-btn
                    flat
                    label="Clear"
                    @click="schedule = ''"
                    v-close-popup
                  />
                  <q-btn
                    flat
                    label="Add schedule"
                    @click="formatSchedule"
                    v-close-popup
                  />
                </q-card-actions>
              </q-card>
            </q-dialog>
          </q-tab-panel>

          <q-tab-panel name="alarms">
            <div class="text-h6">Alarms</div>
            Lorem ipsum dolor sit amet consectetur adipisicing elit.
          </q-tab-panel>

          <q-tab-panel name="movies">
            <div class="text-h6">Movies</div>
            Lorem ipsum dolor sit amet consectetur adipisicing elit.
          </q-tab-panel>
        </q-tab-panels>

        <q-separator />

        <!-- Tab buttons -->
        <q-tabs
          v-model="tab"
          dense
          :class="$q.dark.isActive ? 'bg-grey-9' : 'bg-grey-3'"
          align="justify"
          narrow-indicator
        >
          <q-tab name="submit" label="Import" />
          <q-tab name="alarms" label="Alarms" />
          <q-tab name="movies" label="Movies" />
        </q-tabs>
      </q-card>
    </div>
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
const columns = [
  {
    name: 'provider',
    align: 'left',
    label: 'Provider',
    field: 'provider',
    sortable: true,
  },
  {
    name: 'shift',
    align: 'center',
    label: 'Shift',
    field: 'shift',
    sortable: true,
  },
];
const formattedSchedule = ref([{ provider: 'dolly pardon', shift: '9 to 5' }]);
const formatSchedule = () => {
  if (schedule.value) {
    let str = schedule.value.toString();
    let arr = str.split(/\r?\t?\n|\r|\n/g);
    let pairs = [];
    for (let i = 0; i < arr.length + -1; i += 2) {
      let provider = arr[i];
      let shift = arr[i + 1];
      if (shift.includes('STO')) {
        continue;
      }
      let row = {
        provider,
        shift,
      };
      pairs.push(row);
    }
    formattedSchedule.value = pairs;
    prompt.value = false;
    step1input.value = true;
  } else {
    formattedSchedule.value = [{ provider: 'dolly pardon', shift: '9 to 5' }];
  }
};
const ft = ref(false);
const min = -5;
const max = 5;
const prompt = ref(false);
const step1 = ref(false);
const step1input = ref(false);
const pagination = {
  rowsPerPage: 0,
};
const scheduleDate = ref('');
const tab = ref('submit');
</script>

<style lang="sass"></style>
