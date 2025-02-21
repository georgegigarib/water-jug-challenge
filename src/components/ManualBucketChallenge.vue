<template>
  <div class="container">
    <!-- Success message -->
    <div v-if="success" class="success-message">
      <h5>You successfully obtained {{ goal }}L in {{ steps.length }} steps!</h5>
      <button @click="reset">Reset</button>
    </div>
    <!-- Error message -->
    <div v-if="hasError" class="error-message">
      <h5>{{ errorMessage }}</h5>
      <button @click="reset">Reset</button>
    </div>

    <h1>Water Jug Challenge</h1>

    <div class="input-group">
      <label>
        Jug 1 (L):
        <input v-model.number="jug1Capacity" type="number" min="1" max="100" @input="validateInputs" />
      </label>
      <label>
        Jug 2 (L):
        <input v-model.number="jug2Capacity" type="number" min="1" max="100" @input="validateInputs" />
      </label>
      <label>
        Goal (L):
        <input v-model.number="goal" type="number" min="1" max="100" @input="validateInputs" />
      </label>
    </div>

    <div class="jug-status">
      <p>Jug 1: {{ jug1 }}L / {{ jug1Capacity }}L</p>
      <p>Jug 2: {{ jug2 }}L / {{ jug2Capacity }}L</p>
    </div>

    <div class="button-group">
      <button @click="fillJug1">Fill Jug 1</button>
      <button @click="fillJug2">Fill Jug 2</button>
      <button @click="emptyJug1">Empty Jug 1</button>
      <button @click="emptyJug2">Empty Jug 2</button>
      <button @click="pourJug1ToJug2">Pour Jug 1 → Jug 2</button>
      <button @click="pourJug2ToJug1">Pour Jug 2 → Jug 1</button>
      <button @click="reset" class="reset-button">Reset</button>
    </div>

    <div class="log">
      <h3>Steps:</h3>
      <ul>
        <li v-for="(step, index) in steps" :key="index">{{ step }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";

import '../bucket-style.css'

const jug1Capacity = ref(5);
const jug2Capacity = ref(3);
const goal = ref(4);

const jug1 = ref(0);
const jug2 = ref(0);

const steps = ref<string[]>([]);

const hasError = ref(false);
const errorMessage = ref("");

// Compute success condition
const success = computed(() => jug1.value === goal.value || jug2.value === goal.value);

// Calculate (GCD)
const gcd = (a: number, b: number): number => (b === 0 ? a : gcd(b, a % b));


const cleanErrors = () => {
  hasError.value = false
  errorMessage.value = 'false'
}

// Validate inputs
const validateInputs = (): boolean => {
  cleanErrors()
  if (jug1Capacity.value === jug2Capacity.value) {
    errorMessage.value = "Error: Both jugs cannot have the same capacity.";
    hasError.value = true;
    return false;
  }
  if (goal.value > Math.max(jug1Capacity.value, jug2Capacity.value)) {
    errorMessage.value = `Error: The goal (${goal.value}L) cannot be larger than the maximum jug capacity.`;
    hasError.value = true;
    return false;
  }
  const divisor = gcd(jug1Capacity.value, jug2Capacity.value);
  if (goal.value % divisor !== 0) {
    errorMessage.value = `Error: It is not possible to obtain ${goal.value}L with these jugs because the goal is not divisible by the GCD of the jug capacities (${divisor}).`;
    hasError.value = true;
    return false;
  }
  hasError.value = false;
  errorMessage.value = "";
  return true;
};

//6 different possible movements

const fillJug1 = () => {
  jug1.value = jug1Capacity.value;
  steps.value.push(`Filled Jug 1 (${jug1Capacity.value}L)`);
};

const fillJug2 = () => {
  jug2.value = jug2Capacity.value;
  steps.value.push(`Filled Jug 2 (${jug2Capacity.value}L)`);
};

const emptyJug1 = () => {
  jug1.value = 0;
  steps.value.push("Emptied Jug 1");
};

const emptyJug2 = () => {
  jug2.value = 0;
  steps.value.push("Emptied Jug 2");
};

const pourJug1ToJug2 = () => {
  const transfer = Math.min(jug1.value, jug2Capacity.value - jug2.value);
  jug1.value -= transfer;
  jug2.value += transfer;
  steps.value.push(`Poured ${transfer}L from Jug 1 to Jug 2`);
};

const pourJug2ToJug1 = () => {
  const transfer = Math.min(jug2.value, jug1Capacity.value - jug1.value);
  jug2.value -= transfer;
  jug1.value += transfer;
  steps.value.push(`Poured ${transfer}L from Jug 2 to Jug 1`);
};

const reset = () => {
  jug1Capacity.value = 0;
  jug2Capacity.value = 0;
  goal.value = 1;
  steps.value = [];
  hasError.value = false;
  errorMessage.value = "";
};
</script>
