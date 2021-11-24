<template>
  <v-app>
    <v-parallax
      height="300"
      src="https://cdn.vuetifyjs.com/images/parallax/material.jpg"
      ><v-row>
        <v-spacer></v-spacer>
        <div class="text-h4 mt-15">BMI Dashboard</div>
        <v-spacer></v-spacer
      ></v-row>
    </v-parallax>

    <v-main>
      <v-container>
        <v-card class="pa-7 mt-7" outlined>
          <v-row class="text-center">{{ patient.name[0].family }}</v-row>
          <v-row class="text-center">{{ patient.birthDate }}</v-row>
        </v-card>
        <v-card class="pa-7 mt-7" outlined>
          <v-row>
            <v-slider
              v-model="age"
              color="orange"
              label="Age"
              min="18"
              max="100"
              thumb-label
            ></v-slider>
          </v-row>
          <v-row>
            <v-slider
              v-model="weight"
              color="green"
              label="Weight"
              min="20"
              max="200"
              thumb-label
            ></v-slider>
          </v-row>
          <v-row>
            <v-slider
              v-model="height"
              color="blue"
              label="Height"
              min="140"
              max="200"
              thumb-label
            ></v-slider>
          </v-row>

          <v-row>
            <v-col>
              <v-row>Age: {{ age }}</v-row>
              <v-row>Weight: {{ weight }}</v-row>
              <v-row>Height: {{ height }}</v-row>
            </v-col>
            <v-col>
              <v-row>Ihr BMI ist: {{ bmi }}</v-row>
              <v-row>
                <v-icon v-if="bmi > 18.5 && bmi <= 25" large color="green"
                  >mdi-emoticon-happy-outline</v-icon
                >
                <v-icon v-else-if="bmi <= 18.5" color="yellow" large
                  >mdi-emoticon-neutral-outline</v-icon
                >
                <v-icon v-else-if="bmi > 25 && bmi <= 30" large color="yellow"
                  >mdi-emoticon-happy-outline</v-icon
                >
                <v-icon v-else-if="bmi > 30 && bmi <= 35" large color="yellow"
                  >mdi-emoticon-sad-outline</v-icon
                >
                <v-icon v-else-if="bmi > 35 && bmi < 40" large color="orange"
                  >mdi-emoticon-sad-outline</v-icon
                >
                <v-icon v-else large color="red"
                  >mdi-emoticon-sad-outline</v-icon
                >
              </v-row>
            </v-col>
            <v-col>
              <v-btn @click="addBMI(bmi)" elevation="2">Add</v-btn>
            </v-col>
            <v-sparkline
              class="mt-10"
              :value="value"
              :gradient="gradient"
              :smooth="radius || false"
              :padding="padding"
              :line-width="width"
              :stroke-linecap="lineCap"
              :gradient-direction="gradientDirection"
              :fill="fill"
              :type="type"
              :auto-line-width="autoLineWidth"
              auto-draw
            ></v-sparkline>
          </v-row>
        </v-card>
        <v-row class="mt-7">
          <v-col>
            <v-sheet height="500">
              <v-calendar :now="today" :value="today" color="primary">
                <template v-slot:day="{ past, date }">
                  <v-row class="fill-height">
                    <template v-if="past && tracked[date]">
                      <v-sheet
                        v-for="(percent, i) in tracked[date]"
                        :key="i"
                        :title="category[i]"
                        :color="colors[i]"
                        :width="`${percent}%`"
                        height="100%"
                        tile
                      ></v-sheet>
                    </template>
                  </v-row>
                </template>
              </v-calendar>
            </v-sheet>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  components: {},

  data: () => ({
    width: 2,
    radius: 10,
    padding: 8,
    lineCap: "round",
    gradient: ["#58e4d4", "#00bad1", "#ff6c6c"],
    value: [2, 5, 9, 5, 10, 3, 5, 0, 0, 1, 8, 2, 9],
    gradientDirection: "top",

    fill: false,
    type: "trend",
    autoLineWidth: false,
    today: new Date(),
    tracked: {
      "2019-01-09": [23, 45, 10],
      "2019-01-08": [10],
      "2019-01-07": [0, 78, 5],
      "2019-01-06": [0, 0, 50],
      "2019-01-05": [0, 10, 23],
      "2019-01-04": [2, 90],
      "2019-01-03": [10, 32],
      "2019-01-02": [80, 10, 10],
      "2019-01-01": [20, 25, 10],
    },
    colors: ["#1867c0", "#fb8c00", "#000000"],
    category: ["Development", "Meetings", "Slacking"],

    age: 18,
    weight: 20,
    height: 140,
    patient: {
      resourceType: "Patient",
      id: "eed581f6-6830-4ca2-9e17-8b3aba3b82fb",
      meta: {
        profile: [
          "https://fhir.kbv.de/StructureDefinition/KBV_PR_MIO_MR_Patient_Mother|1.0.0",
        ],
      },
      identifier: [
        {
          type: {
            coding: [
              {
                system: "http://fhir.de/CodeSystem/identifier-type-de-basis",
                code: "GKV",
              },
            ],
          },
          system: "http://fhir.de/NamingSystem/gkv/kvid-10",
          value: "3094897682",
        },
      ],
      name: [
        {
          use: "official",
          family: "Mustermann",
          _family: {
            extension: [
              {
                url: "http://hl7.org/fhir/StructureDefinition/humanname-own-name",
                valueString: "Mustermann",
              },
            ],
          },
          given: ["Marin"],
        },
      ],
      birthDate: "1990-05-01",
      address: [{ type: "both", city: "Berlin", country: "DE" }],
    },
  }),
  methods: {
    addBMI(value) {
      this.value.shift();
      this.value[this.value.length] = value;
    },
  },

  computed: {
    bmi() {
      let computed_height = (this.height / 100) * (this.height / 100);
      return Math.floor((this.weight / computed_height) * 10) / 10;
    },
  },
};
</script>
