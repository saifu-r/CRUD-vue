<template>
  <base-card>
    <base-button @click="showDetails">Load Details</base-button>
    <div class="name">

        <ul v-for="patient in patients" :key="patient.id">
            <div class="details">
                <h2>{{ patient.name }}</h2>
                <li><strong>Age: </strong>{{ patient.age }}</li>
                <li><strong>Gender: </strong>{{ patient.gender }}</li>
                <li><strong>Disease: </strong><small v-for="(disease,index) in patient.disease" :key="disease">{{index+1 +') '+disease +' '}}</small></li>
            </div>
            <div class="delete-button">
                <base-button>Delete</base-button>
            </div>
        </ul>
     
    </div>
  </base-card>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from "vue";
import axios from "axios";
import Patient from "@/types/Patient";

export default defineComponent({
  setup() {
    const patients = ref<Patient[]>([]);
    const showDetails = async () => {
      try {
        patients.value = [];
        const response = await axios.get(
          "https://vue-crud-cdad1-default-rtdb.firebaseio.com/patients.json"
        );

        for (const key in response.data) {
          const patient: Patient = {
            id: key,
            name: response.data[key].name,
            age: response.data[key].age,
            gender: response.data[key].gender,
            disease: response.data[key].disease,
          };
          patients.value.push(patient);
          console.log(response);
        }
      } catch (error) {
        alert("error fetching members data!!");
      }
    };

    onMounted(() => {
      showDetails();
    });

    return { showDetails, patients };
  },
});
</script>

<style scoped>
ul {
  border: 0.1rem solid black;
  border-radius: 10px;
  list-style: none;
  padding: 1.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  
}
ul h2 {
  margin: 0;
}
</style>