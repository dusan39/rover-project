<script setup>
import { ref, computed } from 'vue';

  const board = ref([
    ['', '', '', ''],
    ['', '', '', ''],
    ['', '', '', ''],
    ['', '', '', ''],
  ]);

  let roverCommands = ref('');
  let roverPosition = ref('');
  let commandsArray = [];
  let positionArray = [];

  let isPositionSet = ref(false);
  let disabled = ref(false);

  let roverX = ref(0); 
  let roverY = ref(0);
  const obstacleX = ref(2);
  const obstacleY = ref(2);

  const obstacle = (x, y) => {
    return x === obstacleX.value && y === obstacleY.value
  };

  const addCommand = () => {
    const parts = roverCommands.value.split('-');
    commandsArray = commandsArray.concat(parts);

    function moveRover() {
      if (commandsArray.length > 0) {
        const command = commandsArray.shift();
        switch (command) {
          case 'n':
            roverX.value = (roverX.value - 1 + 4) % 4;
            break;
          case 's':
            roverX.value = (roverX.value + 1) % 4;
            break;
          case 'e':
            roverY.value = (roverY.value + 1) % 4;
            break;
          case 'o':
            roverY.value = (roverY.value - 1 + 4) % 4;
            break;
          default:
            alert(`Invalid command: ${command}`);
            break;
        }
        setTimeout(moveRover, 1000);
      } else {
        commandsArray = [];
        roverCommands.value = '';
      }
    }
    moveRover();
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

  const shouldDisplayRover = computed(() => {
    const roverAtObstacle = roverX.value === obstacleX.value && roverY.value === obstacleY.value;

    if (roverAtObstacle) {
      alert('There is an obstacle!');
      retry()
    }

    return (x, y) => {
      return !roverAtObstacle && x === roverX.value && y === roverY.value;
    };
  });

  const retry = () => {
    roverX.value = null
    roverY.value = null
    commandsArray = [];
    positionArray = [];
    roverPosition.value = '';
    roverCommands.value = '';
    disabled.value = false
    isPositionSet.value = false
  }

</script>

<template>
  <div>
    <main class="pt-8 text-center bg-[url('./assets/mars.jpg')] min-h-screen">

      <div class="flex justify-around">
        <div class="mb-10 flex justify-center flex-col">
          <input class="w-60 mx-auto mb-5" maxlength="3" v-model="roverPosition" placeholder="Enter rover's position" />
          <h3 class="mb-5 text-white">Example = 1-1</h3>
          <button class="w-50 mx-auto border border-gray-100 text-xl px-10 py-5 rounded-2xl text-white hover:bg-orange-600 transition ease-in-out delay-50 cursor-pointer" @click="addPosition" :disabled="roverPosition.trim() === '' || disabled === true">Set position</button>
        </div>

        <div class="mb-10 flex justify-center flex-col">
          <input class="w-60 mx-auto mb-5" maxlength="11" v-model="roverCommands" placeholder="Enter rover's comands" />
          <h3 class="mb-5 text-white">Example = n-e-s-o-o</h3>
          <button class="w-50 mx-auto border border-gray-100 text-xl px-10 py-5 rounded-2xl text-white hover:bg-orange-600 transition ease-in-out delay-50 cursor-pointer" @click="addCommand" :disabled="roverCommands.trim() === '' || disabled === true">Submit comands</button>
        </div>
      </div>
      
      <div class="flex flex-col items-center mb-8">
      <div v-for="(row, x) in board" class="flex">
        <div v-for="(cell, y) in row" 
            :class="[
              'border border-white-800 w-20 h-20 flex items-center justify-center material-icons-outlined text-4xl',
              { 'bg-image-box': obstacle(x, y) }, { 'bg-image-box': shouldDisplayRover(x, y) }
            ]">
          <img v-if="obstacle(x, y)" src="./assets/obstacle.svg" alt="Obstacle" class="w-14 h-14">
          <img v-if="shouldDisplayRover(x, y) && isPositionSet === true" src="./assets/rover.svg" alt="Rover" class="w-14 h-14">
        </div>
      </div>
    </div>

    </main>  
  </div>
</template>

<style scoped>

</style>