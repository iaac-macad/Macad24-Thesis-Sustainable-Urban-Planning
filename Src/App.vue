<template>
    <div>
      <h1>Homework Component</h1>
      <toggle-input v-model="toggleValue"></toggle-input>
      <dropdown-input v-model="selectedOption" :options="options"></dropdown-input>
    </div>
  </template>
  
  <script>
  import ToggleInput from './ToggleInput.vue';
  import DropdownInput from './DropdownInput.vue';
  
  export default {
    components: {
      ToggleInput,
      DropdownInput
    },
    data() {
      return {
        toggleValue: false,
        selectedOption: null,
        options: ['Option 1', 'Option 2', 'Option 3']
      };
    }
  }
  </script>
  
  <style scoped>
  </style>
  