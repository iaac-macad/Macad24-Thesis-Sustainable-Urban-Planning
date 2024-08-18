<script setup>
import { ref, onBeforeMount, computed } from "vue"
import { loadRhino } from "@/scripts/compute.js"

// Import other Vue components in order to add them to a template.
import Header from "commonComponents/Header.vue"
import GeometryView from "./components/GeometryView.vue"
import SliderInput from "./components/SliderInput.vue"
// import ToggleInput from "commonComponents/ToggleInput.vue"
import DropdownSelector from "./components/DropdownSelector.vue"
import ComputeButton from "./components/ComputeButton.vue"

import def from './assets/Eco.gh' //import Grasshopper definition for assets

let firstSliderName = ref("Length") //must match the Input name in your GH definition!
let firstSliderValue = ref(10) //default slider value

let secondSliderName = ref("Width") //must match the Input name in your GH definition!
let secondSliderValue = ref(10) //default slider value

let thirdSliderName = ref("Count of floors") //must match the Input name in your GH definition!
let thirdSliderValue = ref(5) //default slider value

let fourthSliderName = ref("Number of Windows") //must match the Input name in your GH definition!
let fourthSliderValue = ref(5) //default slider value

let fifthSliderName = ref("Floor Height") //must match the Input name in your GH definition!
let fifthSliderValue = ref(4) //default slider value

let dropdownName = ref("Colours")
let dropdownIndex = ref(3)

const dropdownOptions = [
  { label: "Concrete", value: 11 },
  { label: "Wood", value: 57 },
  { label: "Steel", value: 1 },
];

let encodedFile = ref(null);
let isButtonDisabled = ref(false)

///.............................................
let path = def //path to the Grasshopper definition
let data = ref({})
let metadata = ref([])
let compute = ref(false)

let length = ref(3)
let width = ref(3)
let floors = ref(15)
const total_m_each_floor = computed(() => length.value * width.value)
let price = ref(null)


function calculatePrice() {
  price.value = total_m_each_floor.value * floors.value * 1000
}

function updateValue(newValue, parameterName) {

  if (parameterName === firstSliderName.value) {
    firstSliderValue.value = newValue
    length.value = newValue
  }

  if (parameterName === secondSliderName.value) {
    secondSliderValue.value = newValue
    width.value = newValue
  }

  if (parameterName === thirdSliderName.value) {
    thirdSliderValue.value = newValue
    floors.value = newValue
  }

  if (parameterName === fourthSliderName.value) {
    fourthSliderValue.value = newValue
  }

  if (parameterName === fifthSliderName.value) {
    fifthSliderValue.value = newValue
  }

  else if (parameterName === dropdownName.value) {
    dropdownIndex.value = newValue
  }

  console.log(parameterName + " : " + newValue)
}


// function update3dmData(newData) {
//   encodedFile.value = newData
//   isButtonDisabled.value = false
//   compute.value = true

// }

// Example state for the inputs
const numberOfWindows = ref(0)
const material = ref('concrete') // Default material

function resetValues() {
  length.value = 0
  width.value = 0
  floors.value = 0
  numberOfWindows.value = 0
  material.value = 'concrete'
}


function receiveMetadata(newValue) {
  console.log(newValue)
  metadata.value = newValue
}

function runCompute(newVal) {
  compute.value = newVal
}

// a computed ref. Vue will keep track of this and update it
const computeData = computed(() => {
  data = {
    ["encodedFile"]: encodedFile.value,
    [firstSliderName.value]: Number(firstSliderValue.value),
    [secondSliderName.value]: Number(secondSliderValue.value),
    [thirdSliderName.value]: Number(thirdSliderValue.value),
    [fourthSliderName.value]: Number(fourthSliderValue.value),
    [fifthSliderName.value]: Number(fifthSliderValue.value),
    
    [dropdownName.value]: Number(dropdownIndex.value),
  };

  return data
})

</script>

<style>
  .logo {
    width: 100%; /* Adjust this percentage as needed */
    height: auto; /* Keeps the aspect ratio */
    display: block;
    margin: 0 auto;
  }
</style>

<template>
  <div id="sidebar" class="container">
    <h3>Sustainable Urban Planning</h3>
    <img src="./assets/Galt.png" alt="Logo" class="logo" />
    <p>A Transformative Approach to ...
    </p>


    <SliderInput :title="firstSliderName" :min="1" :max="50" :step="1" :val="firstSliderValue" @update="updateValue">
    </SliderInput>
    <SliderInput :title="secondSliderName" :min="1" :max="50" :step="1" :val="secondSliderValue" @update="updateValue">
    </SliderInput>
    <SliderInput :title="thirdSliderName" :min="1" :max="40" :step="1" :val="thirdSliderValue" @update="updateValue">
    </SliderInput>
    <SliderInput :title="fourthSliderName" :min="2" :max="12" :step="1" :val="fourthSliderValue" @update="updateValue">
    </SliderInput>
      <SliderInput :title="fifthSliderName" :min="2" :max="6" :step="1" :val="fifthSliderValue" @update="updateValue">
    </SliderInput>
    

    <p> Metadata information = {{ metadata }}</p>

    <!-- <ToggleInput :title="toggleName" :val="true" @update="updateValue"></ToggleInput> -->

    <DropdownSelector :title="dropdownName" :options="dropdownOptions" :val="dropdownIndex" @update="updateValue" />
    <button @click="calculatePrice">Price</button>
      <div v-if="price !== null">
        Price for each square meter: 1000 €<br>
        Square meter for each floor: {{ total_m_each_floor }}<br>
        Number of floors: {{ floors }}<br>
        Price: {{ price }}<br>
      </div>
    <ComputeButton title="Compute" @click="runCompute" :isDisabled="isButtonDisabled" />
  </div>

  <div id="viewer" class="container">
    <GeometryView :data="computeData" :path="path" :runCompute="compute" @updateMetadata="receiveMetadata" />
  </div>
</template>
  
 
  
<style scoped>

.container{
background-color: rgb(37, 37, 37);
border-style: dotted;
border-width: 1px;
}

#content {

  display: flex;
  height: calc(100vh - 68px);
}

#sidebar {

  width: 310px;
  padding: 10px;
  background-color: rgba(194, 206, 247, 0.866);

}

#viewer {
  width: calc(100% - 310px);
}

.modern-range {
  -webkit-appearance: none;
  width: 100%;
  background: linear-gradient(90deg, #ffffff, #ff1900);
  height: 17px;
  border-radius: 15px;
  margin: 10px 0px;
}

.modern-range::-webkit-slider-thumb {
  -webkit-appearance: none;
  height: 15px;
  width: 15px;
  border-radius: 15px;
  background-color: rgb(86, 247, 23);
  cursor: pointer;
}

</style>

