<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <v-spacer />
      <div class="text-h4">BMI Dashboard</div>
      <v-spacer />
    </v-app-bar>

    <v-main>
      <v-container>
        <v-card class="pa-7 mt-7" outlined>
          <v-row class="text-center">{{patient.name[0].family}}</v-row>
          <v-row class="text-center">{{patient.birthDate}}</v-row>
        </v-card>
        <v-card class="pa-7 mt-7" outlined>
          <v-row>
            <v-slider v-model="age" color="orange" label="Age" min="18" max="100" thumb-label></v-slider>
          </v-row>
          <v-row>
            <v-slider v-model="weight" color="green" label="Weight" min="20" max="200" thumb-label></v-slider>
          </v-row>
          <v-row>
            <v-slider v-model="height" color="blue" label="Height" min="140" max="200" thumb-label></v-slider>
          </v-row>
          <v-row>
            <v-col>
              <v-row>Age: {{age}}</v-row>
              <v-row>Weight: {{weight}}</v-row>
              <v-row>Height: {{height}}</v-row>
            </v-col>
            <v-col>
              <v-row>Ihr BMI ist: {{bmi}}</v-row>
              <v-row>
                <v-icon v-if="bmi>18.5 && bmi<=25" large color="green">mdi-emoticon-happy-outline</v-icon>
                <v-icon v-else-if="bmi<=18.5" color="yellow" large>mdi-emoticon-neutral-outline</v-icon>
                <v-icon
                  v-else-if="bmi>25 && bmi<=30"
                  large
                  color="yellow"
                >mdi-emoticon-happy-outline</v-icon>
                <v-icon v-else-if="bmi>30 && bmi<=35" large color="yellow">mdi-emoticon-sad-outline</v-icon>
                <v-icon v-else-if="bmi>35 && bmi<40" large color="orange">mdi-emoticon-sad-outline</v-icon>
                <v-icon v-else large color="red">mdi-emoticon-sad-outline</v-icon>
              </v-row>
            </v-col>
          </v-row>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  components: {},

  data: () => ({
    age: 18,
    weight: 20,
    height: 140,
    patient: {
      resourceType: "Patient",
      id: "eed581f6-6830-4ca2-9e17-8b3aba3b82fb",
      meta: {
        profile: [
          "https://fhir.kbv.de/StructureDefinition/KBV_PR_MIO_MR_Patient_Mother|1.0.0"
        ]
      },
      identifier: [
        {
          type: {
            coding: [
              {
                system: "http://fhir.de/CodeSystem/identifier-type-de-basis",
                code: "GKV"
              }
            ]
          },
          system: "http://fhir.de/NamingSystem/gkv/kvid-10",
          value: "3094897682"
        }
      ],
      name: [
        {
          use: "official",
          family: "Mustermann",
          _family: {
            extension: [
              {
                url:
                  "http://hl7.org/fhir/StructureDefinition/humanname-own-name",
                valueString: "Mustermann"
              }
            ]
          },
          given: ["Marin"]
        }
      ],
      birthDate: "1990-05-01",
      address: [{ type: "both", city: "Berlin", country: "DE" }]
    }
  }),
  methods: {},
  computed: {
    bmi() {
      let computed_height = (this.height / 100) * (this.height / 100);
      return Math.floor((this.weight / computed_height) * 10) / 10;
    }
  }
};
</script>
