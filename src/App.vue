<script setup>
import { ref, computed } from 'vue';

  const board = ref([
    ['', '', '', ''],
    ['', '', '', ''],
    ['', '', '', ''],
    ['', '', '', ''],
  ]);

  let roverComands = ref('');
  let roverPosition = ref('');
  let comandsArray = [];
  let positionArray = [];
  const isPositionSet = ref(false);
  const roverX = ref(0); 
  const roverY = ref(0); 

  const obstacle = (x, y) => {
    return x === 0 && y === 0;
  };

  const addComand = () => {
    const parts = roverComands.value.split('-');
    comandsArray = comandsArray.concat(parts);
    roverComands.value = '';
    console.log(comandsArray)
  };

  const addPosition = () => {
    const parts = roverPosition.value.split('-');
    if (parts.length === 2) {
      const x = parseInt(parts[0]);
      const y = parseInt(parts[1]);
      if (!isNaN(x) && !isNaN(y)) {
        positionArray = [x, y];
        roverX.value = x; 
        roverY.value = y; 
        isPositionSet.value = true;
      }
    }
    roverPosition.value = '';
  };

  const shouldDisplayRover = computed(() => (x, y) => {
    return x === roverX.value && y === roverY.value;
  });

</script>

<template>
  <div>
    <main class="pt-8 text-center bg-gray-800 min-h-screen">

      <div class="flex justify-around">
        <div class="mb-10 flex justify-center flex-col">
          <input class="w-60 mx-auto mb-5" maxlength="3" v-model="roverPosition" placeholder="Enter rover's position" />
          <h3 class="mb-5 text-white">Example = 1-1</h3>
          <button class="w-50 mx-auto border border-gray-100 text-xl px-10 py-5 rounded-2xl text-white" @click="addPosition" :disabled="roverPosition.trim() === ''">Set position</button>
        </div>

        <div class="mb-10 flex justify-center flex-col">
          <input class="w-60 mx-auto mb-5" maxlength="9" v-model="roverComands" placeholder="Enter rover's comands" />
          <h3 class="mb-5 text-white">Example = f-f-l-b-r</h3>
          <button class="w-50 mx-auto border border-gray-100 text-xl px-10 py-5 rounded-2xl text-white" @click="addComand" :disabled="roverComands.trim() === ''">Submit comands</button>
        </div>
      </div>

      <div class="flex flex-col items-center mb-8">
      <div v-for="(row, x) in board" class="flex">
        <div v-for="(cell, y) in row" 
            :class="[
              'border border-gray-800 w-20 h-20 flex items-center justify-center material-icons-outlined text-4xl dark:border-white',
              { 'bg-image-box': obstacle(x, y) }, { 'bg-image-box': shouldDisplayRover(x, y) }
            ]">
          <img v-if="obstacle(x, y)" src="./assets/obstacle.svg" alt="Obstacle" class="w-14 h-14">
          <img v-if="shouldDisplayRover(x, y) && isPositionSet === true" src="./assets/rover.svg" alt="Rover" class="w-14 h-14">
        </div>
      </div>
    </div>

    <button class="w-40 mx-auto border border-gray-100 text-xl px-10 py-5 rounded-2xl text-white">Retry</button>

    </main>  
  </div>
</template>

<style scoped>

</style>