<script>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import { ref, computed, watch, onMounted } from 'vue';
import random from 'random';

export default {
  setup() {
    const parts = ['red', 'blue', 'yellow', 'green'];
    const getRandomColor = () => {
      const index = random.int(0, parts.length - 1);
      return parts[index];
    };

    const watchMode = ref(true);
    const pointer = ref(0);
    const randomEvents = ref(['red', 'blue']);

    const score = computed(() => {
      return randomEvents.value.length - 1;
    });

    const isGameOver = computed(() => {
      return pointer.value === -1;
    });

    const currentColor = computed(() => {
      return randomEvents.value[pointer.value];
    });

    watch(
      [pointer, randomEvents],
      ([pointerValue]) => {
        if (watchMode.value) {
          if (pointerValue < randomEvents.value.length) {
            setTimeout(() => (pointer.value = pointerValue + 1), 2000);
          } else {
            pointer.value = 0;
            watchMode.value = false;
          }
        }
      },
      { immediate: true }
    );

    const handleClickTriangle = (color) => {
      if (currentColor.value === color) {
        if (pointer.value === randomEvents.value.length - 1) {
          pointer.value = 0;
          watchMode.value = true;
          randomEvents.value = [...randomEvents.value, getRandomColor()];
        } else {
          pointer.value += 1;
        }
      } else {
        pointer.value = -1;
      }
    };

    const direction = (color) => {
  const baseClass = "position-absolute rounded-0 ";
  switch (color) {
    case 'red':
      return `${baseClass} top-0 start-0 w-50 h-50 ${
        currentColor.value === color && watchMode.value ? 'light-up bg-danger' : 'bg-danger'
      }`;
    case 'blue':
      return `${baseClass} top-0 end-0 w-50 h-50 ${
        currentColor.value === color && watchMode.value ? 'light-up bg-primary' : 'bg-primary'
      }`;
    case 'yellow':
      return `${baseClass} bottom-0 start-0 w-50 h-50 ${
        currentColor.value === color && watchMode.value ? 'light-up bg-warning' : 'bg-warning'
      }`;
    case 'green':
      return `${baseClass} bottom-0 end-0 w-50 h-50 ${
        currentColor.value === color && watchMode.value ? 'light-up bg-success' : 'bg-success'
      }`;
    default:
      return '';
  }
};

    onMounted(() => {
      console.log('mounted');
    });

    return {
      watchMode,
      pointer,
      randomEvents,
      score,
      isGameOver,
      currentColor,
      handleClickTriangle,
      direction,
      parts,
    };
  },
};
</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
    </div>
  </header>

  <main>
    <TheWelcome />
    <div class="App">
    <div class="card bg-black game-container">
      <h1 v-if="isGameOver" class="text-center text-white mt-4">Game Over! Your Score is {{ score }}</h1>
      <div class="d-flex flex-wrap align-items-center justify-content-center button-container" v-show="!isGameOver">
        <button
          v-for="(color, index) in parts"
          :key="index"
          :class="direction(color)"
          :color="color"
          :disabled="watchMode"
          @click="handleClickTriangle(color)"
        ></button>
      </div>
    </div>
  </div>
  </main>
</template>

<!-- <style scoped> -->

<style>
@import 'bootstrap/dist/css/bootstrap.min.css';

.game-container {
  position: relative;
  width: 300px;
  height: 300px;
  margin: 0 auto;
}

.button-container {
  position: relative;
  width: 100%;
  height: 100%;
}

.light-up {
  opacity: 0.6; /* you can modify this as needed */
}
</style>
