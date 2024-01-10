<template>
  <base-dialog v-if="openEditDialog" title="Edit Details" @close="confirmError">
    <template #default>
      <div v-for="patient in patients" :key="patient.id">
        <div v-if="patient.id === patientIdTest">
          <div class="name-input">
            <label for="name">Name: </label>
            <input
              type="text"
              id="name"
              class="name"
              :value="patient.name"
              @input="editedName"
            />
          </div>

          <div class="age-input">
            <label for="age">Age: </label>
            <input
              type="number"
              id="age"
              class="age"
              min="1"
              max="100"
              :value="patient.age"
              @input="editedAge"
            />
          </div>

          <div class="gender-input">
            <label>Gender:</label>
            <div>
              <input
                type="radio"
                name="gender"
                id="male"
                value="Male"
                v-model="patient.gender"
                @change="editedGender"
              />
              <label for="male">Male</label>
            </div>
            <div>
              <input
                type="radio"
                name="gender"
                id="female"
                value="Female"
                v-model="patient.gender"
                @change="editedGender"
              />
              <label for="female">Female</label>
            </div>
            <div>
              <input
                type="radio"
                name="gender"
                id="others"
                value="Others"
                v-model="patient.gender"
                @change="editedGender"
              />
              <label for="others">Others</label>
            </div>
          </div>

          <div class="disease-input">
            <label>Disease:</label>
            <div>
              <input
                type="checkbox"
                name="disease"
                id="brain"
                value="Brain"
                v-model="patient.disease"
                @change="editedDisease"
              />
              <label for="brain">Brain</label>
            </div>
            <div>
              <input
                type="checkbox"
                name="disease"
                id="heart"
                value="Heart"
                v-model="patient.disease"
                @change="editedDisease"
              />
              <label for="heart">Heart</label>
            </div>
            <div>
              <input
                type="checkbox"
                name="disease"
                id="lungs"
                value="Lungs"
                v-model="patient.disease"
                @change="editedDisease"
              />
              <label for="lungs">Lungs</label>
            </div>
            <div>
              <input
                type="checkbox"
                name="disease"
                id="kidney"
                value="Kidney"
                v-model="patient.disease"
                @change="editedDisease"
              />
              <label for="kidney">Kidney</label>
            </div>
          </div>
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
          <base-button mode="flat" @click="openDialog(patient.id)"
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
import Disease from "@/types/Disease";

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

    //editing section

    const updatedName = ref("");
    const editedName = (event: Event) => {
      updatedName.value = (event.target as HTMLInputElement).value;
    };
    const updatedAge = ref();
    const editedAge = (event: Event) => {
      updatedAge.value = (event.target as HTMLInputElement).value;
    };

    const updatedGender = ref("");
    const editedGender = (event: Event) => {
      updatedGender.value = (event.target as HTMLInputElement).value;
      // console.log(patients);
    };
    const updatedDisease = ref<Disease[]>([]);
    const editedDisease = (event: Event) => {
      const checkboxValue = (event.target as HTMLInputElement).value as Disease;

      // Toggle the checkbox value in the array
      if (updatedDisease.value.includes(checkboxValue)) {
        updatedDisease.value = updatedDisease.value.filter(
          (disease) => disease !== checkboxValue
        );
      } else {
        updatedDisease.value = [...updatedDisease.value, checkboxValue];
      }

      console.log(updatedDisease.value);
    };

    const patientIdTest = ref("");

    const openEditDialog = ref(false);
    const openDialog = async (key: string) => {
      openEditDialog.value = true;
      patientIdTest.value = key;
      try {
        const response = await axios.put(
          `https://vue-crud-cdad1-default-rtdb.firebaseio.com/patients/${key}.json`
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
      patientIdTest,
      editedName,
      editedAge,
      editedGender,
      editedDisease,
    };
  },
});
</script>

<style scoped>
ul {
  border: 0.05rem solid black;
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

.name-input {
  padding-bottom: 1rem;
}

.name-input input,
.age-input input {
  width: 100%;
}
label {
  margin: 10px 0;
}

input:focus {
  outline: none;
  border-color: #3a0061;
  background-color: #f7ebff;
}

.gender-input,
.disease-input {
  display: flex;
  flex-direction: column;
  padding-bottom: 1rem;
  margin: 0;
}
</style>