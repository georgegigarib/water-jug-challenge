<template>
  <div class="container">
    <div v-if="success" class="success-message">
      <h4>Successfully obtained {{ goal }}L in {{ steps.length }} steps!</h4>
    </div>
    <div v-if="error" class="error-message">
      <h4>{{ errorMessage }}</h4>
    </div>
    <h1>Water Jug Challenge</h1>
    <div class="input-group">
      <label>
        Jug 1 ({{ jug1Capacity }}L):
        <input v-model.number="jug1Capacity" type="number" min="1" max="100" @input="reset" />
      </label>
      <label>
        Jug 2 ({{ jug2Capacity }}L):
        <input v-model.number="jug2Capacity" type="number" min="1" max="100" @input="reset" />
      </label>
      <label>
        Goal ({{ goal }}L):
        <input v-model.number="goal" type="number" min="1" max="100" @input="reset" />
      </label>
    </div>
    <div class="options">
      <label>
        <input type="checkbox" v-model="useDelay" /> Enable step delay (700ms)
      </label>
    </div>
    <div class="jug-status">
      <p>Jug 1: {{ jug1 }}L / {{ jug1Capacity }}L</p>
      <p>Jug 2: {{ jug2 }}L / {{ jug2Capacity }}L</p>
    </div>
    <button @click="solve">Solve Automatically</button>
    <div class="log">
      <h3>Steps:</h3>
      <ul>
        <li v-for="(step, index) in steps" :key="index">{{ (index + 1) + '. ' + step }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";

const jug1Capacity = ref(5);
const jug2Capacity = ref(4);
const goal = ref(3);
const jug1 = ref(0);
const jug2 = ref(0);
const steps = ref<string[]>([]);
const error = ref(false);
const errorMessage = ref("");
const useDelay = ref(true);

// Calculate (GCD)
const gcd = (a: number, b: number): number => (b === 0 ? a : gcd(b, a % b));

// Compute success condition
const success = computed(() => jug1.value === goal.value || jug2.value === goal.value);


// Verify if its solvable
const isSolvable = computed(() => {
  const divisor = gcd(jug1Capacity.value, jug2Capacity.value);
  return goal.value % divisor === 0 && goal.value <= Math.max(jug1Capacity.value, jug2Capacity.value);
});

// Validate inputs
const validateInputs = (): boolean => {
  if (jug1Capacity.value === jug2Capacity.value) {
    errorMessage.value = "Error: Both jugs cannot have the same capacity.";
    return false;
  }
  if (goal.value > Math.max(jug1Capacity.value, jug2Capacity.value)) {
    errorMessage.value = `Error: Goal (${goal.value}L) cannot be larger than the maximum jug capacity.`;
    return false;
  }
  if (!isSolvable.value) {
    errorMessage.value = `Error: It is not possible to obtain ${goal.value}L with these jugs because the goal is not divisible by the GCD of the jug capacities (${gcd(jug1Capacity.value, jug2Capacity.value)}).`;
    return false;
  }
  return true;
};

// Solve the problem
const solve = async () => {
  reset(); // Reset jugs and steps before starting

  // Validate input values
  if (!validateInputs()) {
    error.value = true;
    return;
  }

  let smallCap = Math.min(jug1Capacity.value, jug2Capacity.value);
  let largeCap = Math.max(jug1Capacity.value, jug2Capacity.value);

  // Identify which jug corresponds to each size
  let firstJug = smallCap === jug1Capacity.value ? "Jug 1" : "Jug 2";
  let secondJug = firstJug === "Jug 1" ? "Jug 2" : "Jug 1";

  let small = 0,
    large = 0;

  // Special condition: The goal can be reached with a single transfer
  if (largeCap - smallCap === goal.value) {
    steps.value.push(`Filled ${secondJug} (${largeCap}L)`);
    steps.value.push(`Transferred ${smallCap}L from ${secondJug} to ${firstJug}`);

    // Update jug states accordingly
    jug1.value = jug1Capacity.value < jug2Capacity.value ? smallCap : largeCap - smallCap;
    jug2.value = jug1Capacity.value < jug2Capacity.value ? largeCap - smallCap : smallCap;
    return;
  }

  // Determine which strategy to use
  // - If the difference between the goal and the large jug's capacity is smaller, prioritize the large jug
  // - Otherwise, prioritize the small jug
  const useLargeFirst = Math.abs(goal.value - largeCap) <= Math.abs(goal.value - smallCap);

  while (small !== goal.value && large !== goal.value) {
    if (useLargeFirst) {
      // Prioritize the large jug
      if (large === 0) {
        large = largeCap;
        steps.value.push(`Filled ${secondJug} (${largeCap}L)`);
      } else if (small === smallCap) {
        small = 0;
        steps.value.push(`Emptied ${firstJug}`);
      } else {
        const transfer = Math.min(large, smallCap - small);
        large -= transfer;
        small += transfer;
        steps.value.push(`Transferred ${transfer}L from ${secondJug} to ${firstJug}`);
      }
    } else {
      // Prioritize the small jug
      if (small === 0) {
        small = smallCap;
        steps.value.push(`Filled ${firstJug} (${smallCap}L)`);
      } else if (large === largeCap) {
        large = 0;
        steps.value.push(`Emptied ${secondJug}`);
      } else {
        const transfer = Math.min(small, largeCap - large);
        small -= transfer;
        large += transfer;
        steps.value.push(`Transferred ${transfer}L from ${firstJug} to ${secondJug}`);
      }
    }

    // Update the jug states after each step
    if (jug1Capacity.value < jug2Capacity.value) {
      jug1.value = small;
      jug2.value = large;
    } else {
      jug1.value = large;
      jug2.value = small;
    }

    // Introduce a delay if enabled (for better visualization)
    if (useDelay.value) {
      await new Promise((resolve) => setTimeout(resolve, 700));
    }

    // Exit the loop if the goal is reached
    if (small === goal.value || large === goal.value) break;
  }
};

const reset = () => {
  jug1.value = 0;
  jug2.value = 0;
  steps.value = [];
  error.value = false;
  errorMessage.value = "";
};
</script>