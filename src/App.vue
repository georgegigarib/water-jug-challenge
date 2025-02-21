<script setup lang="ts">
import { ref } from 'vue'
import ManualBucketChallenge from './components/ManualBucketChallenge.vue'
import AutomaticBucketChallenge from './components/AutomaticBucketChallenge.vue'

const isManualModal = ref(false)

const toggleModal = () => {
  isManualModal.value = !isManualModal.value
}
</script>

<template>
  <div>
    <div class="toggle-buttons">
        <button @click="toggleModal" class="toggle-button">
          {{ isManualModal ? 'Show automatic WJC' : 'Show manual WJC' }}
        </button>
      </div>
      
    <div class="container">
      <ManualBucketChallenge v-if="isManualModal" />

      <AutomaticBucketChallenge v-if="!isManualModal" />
    </div>
    
  </div>
</template>

<style scoped>
.toggle-buttons {
  right: 20px;
  top: 50%;
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.toggle-button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
}

.toggle-button:hover {
  background-color: #0056b3;
}
</style>
