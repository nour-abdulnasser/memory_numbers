<template>
  <v-container>
    <div
      style="
        display: flex;
        justify-content: end;
        width: 100%;
        margin-bottom: 20px;
      "
    >
      <input type="checkbox" id="checkbox" v-model="isFormatted" class="me-2" />
      <label for="checkbox">Formatted</label>
    </div>
    <v-progress-linear  :model-value="timePercentage"></v-progress-linear>
    <div
      v-if="showRand"
      onmousedown="return false;"
      onselectstart="return false;"
      class="text-h3 overflow-wrap mx-auto"
    >
      {{ isFormatted ? formatNum(rand) : rand }}
    </div>
    <form
      v-on:submit.prevent="generateRand"
      v-if="showRand && !rand"
      class="text-center"
    >
      <div style="min-height: 200px">
        <v-text-field
          v-model="digits"
          type="text"
          placeholder="Digits"
          class="mt-16"
        />

        <v-text-field
          v-model="seconds"
          type="text"
          placeholder="Time in seconds"
        />
      </div>

      <v-btn width="160" type="submit" class="my-2">Generate</v-btn>
    </form>
    <form v-on:submit.prevent="check" v-if="!showRand" class="text-center">
      <div style="min-height: 200px">
        <v-text-field
          v-model="memorizedNum"
          type="text"
          placeholder="memorized"
          class="mt-16"
        />
      </div>

      <v-btn width="160" type="submit" class="my-2">Check</v-btn>
    </form>
    <div class="text-center">
      <v-btn
        @click="
          rand = '';
          digits = null;
          seconds = null;
          memorizedNum = null;
          showRand = true;
        "
        width="160"
        class="elevation-0 border-opacity-25 border-thin my-2"
      >
        Reset
      </v-btn>
    </div>
  </v-container>
</template>
<script setup>
import { ref, computed } from "vue";
// TODO: Group into 3's
// TODO: Optimize time?
// TODO: Validate
// TODO: No copying.. also from inspect?

// Other features: skip time, reset, history per session?

const grouping = 3;

const digits = ref(null);
const seconds = ref(null);
const rand = ref("");
const memorizedNum = ref(null);
const isFormatted = ref(true);

const showRand = ref(true);
const isCorrect = ref(false);

let timePercentage = ref(0);

const check = () => {
  if (memorizedNum.value == rand.value) {
    isCorrect.value = true;
  } else {
    isCorrect.value = false;
  }
  console.log("eval", isCorrect.value);
};

const generateRand = async () => {
  if (!digits.value) {
    alert("Please enter a valid Digits value");
    return;
  }
  showRand.value = true;
  let num = "";
  for (let d = 0; d < digits.value; d++) {
    const r = Math.floor(Math.random() * 10);
    num = num + r;
  }
  rand.value = num;

  setTimeout(() => {
    showRand.value = false;
  }, seconds.value * 1000);

  const intervalId = setInterval(incrementTime, 1000);
  function incrementTime() {
    if (timePercentage.value <= 100) {
      timePercentage.value += 100/seconds.value;
      console.log(timePercentage.value);
    } else {
      clearInterval(intervalId);
    }
  }
};



const formatNum = (str) => {
  const initIndex = grouping; // index of first space to add
  let spacedStr = "";
  for (let i = 0; i <= str.length - 1; i += initIndex) {
    spacedStr += str.slice(i, i + initIndex) + " ";
  }

  return spacedStr;
};
</script>
<style scoped>
.read-the-docs {
  color: #888;
}
.overflow-wrap {
  overflow-wrap: break-word;
  word-wrap: break-word;
  line-height: 1.5;
}
</style>
