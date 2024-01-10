<template>
  <base-dialog v-if="openEditDialog" title="Edit Details" @close="confirmError">
    <template #default>
        <h1 v-for="patient in patients" :key="patient.id"></h1>
      <div class="name-input">
        <label for="name">Name: </label>
        <input type="text" id="name" class="name" />
      </div>

      <div class="age-input">
        <label for="age">Age: </label>
        <input type="number" id="age" class="age" min="1" max="100" />
      </div>

      <div class="gender-input">
        <label>Gender:</label>
        <div>
          <input type="radio" name="gender" id="male" value="Male" />
          <label for="male">Male</label>
        </div>
        <div>
          <input type="radio" name="gender" id="female" value="Female" />
          <label for="female">Female</label>
        </div>
        <div>
          <input type="radio" name="gender" id="others" value="Others" />
          <label for="others">Others</label>
        </div>
      </div>

      <div class="disease-input">
        <label>Disease:</label>
        <div>
          <input type="checkbox" name="disease" id="brain" value="Brain" />
          <label for="brain">Brain</label>
        </div>
        <div>
          <input type="checkbox" name="disease" id="heart" value="Heart" />
          <label for="heart">Heart</label>
        </div>
        <div>
          <input type="checkbox" name="disease" id="lungs" value="Lungs" />
          <label for="lungs">Lungs</label>
        </div>
        <div>
          <input type="checkbox" name="disease" id="kidney" value="Kidney" />
          <label for="kidney">Kidney</label>
        </div>
      </div>
    </template>

    <template #actions>
      <base-button mode="flat" @click="confirmError">Close</base-button>
      <base-button mode="flat" @click="confirmError">Save</base-button>
    </template>
  </base-dialog>
  <base-card>
    <base-button @click="showDetails">Load Details</base-button>
    <div class="name">
      <ul v-for="patient in patients" :key="patient.id">
        <div class="details">
          <h2>{{ patient.name }}</h2>
          <li><strong>Age: </strong>{{ patient.age }}</li>
          <li><strong>Gender: </strong>{{ patient.gender }}</li>
          <li>
            <strong>Disease: </strong
            ><small
              v-for="(disease, index) in patient.disease"
              :key="disease"
              >{{ index + 1 + ") " + disease + " " }}</small
            >
          </li>
        </div>
        <div class="button">
          <base-button mode="flat" @click="deletePatient(patient.id)"
            >Delete</base-button
          >
          <base-button mode="flat" @click="openDialog(patient)"
            >Edit</base-button
          >
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

    const deletePatient = async (key: string) => {
      // console.log(key);
      try {
        const response = await axios.delete(
          `https://vue-crud-cdad1-default-rtdb.firebaseio.com/patients/${key}.json`
        );
        console.log("Item deleted successfully:", response.data);
        showDetails();
      } catch (error) {
        console.error("Error deleting item:", error);
      }
    };
    const openEditDialog = ref(false);

    const openDialog = async (patient: object) => {
      openEditDialog.value = true;
      try {
        const response = await axios.put(
          `https://vue-crud-cdad1-default-rtdb.firebaseio.com/patients/${patient.id}.json`,
          patient
        );
        showDetails();
      } catch (error) {
        console.error("Error updating patient:", error);
      }
    };

    const confirmError = () => {
      openEditDialog.value = false;
    };

    onMounted(() => {
      showDetails();
    });

    return {
      showDetails,
      patients,
      deletePatient,
      openDialog,
      openEditDialog,
      confirmError,
    };
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

.button {
  display: flex;
  flex-direction: column;
  gap: 0.3rem;
}
</style>