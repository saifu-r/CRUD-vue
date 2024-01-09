<template>
  <base-card>
    <form @submit.prevent="saveDetails">
      <div class="name-input">
        <label for="name">Name: </label>
        <input type="text" id="name" class="name" v-model="enteredName" />
      </div>

      <div class="age-input">
        <label for="age">Age: </label>
        <input
          type="number"
          id="age"
          class="age"
          min="1"
          max="100"
          v-model="enteredAge"
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
            v-model="enteredGender"
          />
          <label for="male">Male</label>
        </div>
        <div>
          <input
            type="radio"
            name="gender"
            id="female"
            value="Female"
            v-model="enteredGender"
          />
          <label for="female">Female</label>
        </div>
        <div>
          <input
            type="radio"
            name="gender"
            id="others"
            value="Others"
            v-model="enteredGender"
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
            v-model="enteredDisease"
          />
          <label for="brain">Brain</label>
        </div>
        <div>
          <input
            type="checkbox"
            name="disease"
            id="heart"
            value="Heart"
            v-model="enteredDisease"
          />
          <label for="heart">Heart</label>
        </div>
        <div>
          <input
            type="checkbox"
            name="disease"
            id="lungs"
            value="Lungs"
            v-model="enteredDisease"
          />
          <label for="lungs">Lungs</label>
        </div>
        <div>
          <input
            type="checkbox"
            name="disease"
            id="kidney"
            value="Kidney"
            v-model="enteredDisease"
          />
          <label for="kidney">Kidney</label>
        </div>
      </div>
      <base-button>Submit</base-button>
    </form>
  </base-card>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import axios from "axios";

export default defineComponent({
  setup() {
    const enteredName = ref("");
    const enteredAge = ref();
    const enteredGender = ref("");
    const enteredDisease = ref([]);

    const saveDetails = () => {
      // console.log(enteredDisease.value)

      axios.post(
        "https://vue-crud-cdad1-default-rtdb.firebaseio.com/patients.json",
        {
          name: enteredName.value,
          age: enteredAge.value,
          gender: enteredGender.value,
          disease: enteredDisease.value,
        }
      );
    };

    return {
      enteredName,
      enteredAge,
      enteredGender,
      enteredDisease,
      saveDetails,
    };
  },
});
</script>

<style scoped>
.form {
  padding: 2rem;
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