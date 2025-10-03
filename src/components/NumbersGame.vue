<template>
  <v-container style="min-height: 100vh">
    <v-progress-linear
      :model-value="timePercentage"
      v-if="showRand && rand"
    ></v-progress-linear>

    <div class="my-5 d-flex w-100 justify-end">
      <input type="checkbox" id="checkbox" v-model="isFormatted" class="me-2" />
      <label for="checkbox" class="courier-font">Formatted</label>
    </div>

    <div
      v-if="showRand"
      onmousedown="return false;"
      onselectstart="return false;"
      class="text-h3 overflow-wrap mx-auto prevent-select"
    >
      {{ isFormatted ? formatNum(rand) : rand }}
    </div>

    <form
      v-on:submit.prevent="generateRand"
      v-if="showRand && !rand"
      class="text-center d-flex flex-column justify-center align-center"
    >
      <div style="" class="w-100">
        <v-text-field
          v-model="digits"
          type="text"
          placeholder="Digits"
          class="w-100"
        />
        <v-text-field
          v-model="seconds"
          type="text"
          class="w-100"
          placeholder="Time in seconds"
        />
      </div>
      <v-btn
        width="160"
        type="submit"
        class="elevation-0 border-opacity-25 border-thin my-2 courier-font"
        >Generate</v-btn
      >
    </form>

    <form
      v-on:submit.prevent="check"
      v-if="!showRand && !showResult"
      class="text-center"
    >
      <div style="">
        <v-text-field
          v-model="memorizedNum"
          type="text"
          placeholder="Enter memorized number"
          class=""
        />
      </div>
      <v-btn width="160" type="submit" class="my-2 courier-font"
        >Show Results</v-btn
      >
    </form>

    <div class="text-center">
      <v-btn @click="reset()" width="160" class="elevation-0 my-2 courier-font">
        Reset
      </v-btn>
    </div>
    <v-row class="results d-flex align-start" v-if="showResult">
      <v-col cols="12" class="text-center d-flex" style="height: fit-content">
        <h2
          :class="{ 'text-success': isCorrect, 'text-red': !isCorrect }"
          class="text-center w-100 my-10"
        >
          {{ isCorrect ? "Well done!" : "It's OK, maybe next time!" }}
        </h2>
      </v-col>
      <div class="w-100 d-flex flex-column flex-md-row">
        <v-col cols="12" md="6">
          <div class="border-thin rounded-lg">
            <div class="text-center border-b mb-5 py-3">
              <h3>Generated Number</h3>
            </div>
            <div class="px-5 pb-5 overflow-wrap">
              {{ isFormatted ? formatNum(rand) : rand }}
            </div>
          </div>
        </v-col>
        <v-col cols="12" md="6" class="flex-grow-1">
          <div class="border-thin rounded-lg fill-height">
            <div class="text-center border-b mb-5 py-3">
              <h3>Your Input</h3>
            </div>
            <div class="px-5 pb-5 overflow-wrap">
              {{ isFormatted ? formatNum(memorizedNum?.replace(/\s/g, "")) : memorizedNum?.replace(/\s/g, "") }}
            </div>
          </div>
        </v-col>
      </div>
    </v-row>
  </v-container>
</template>
<script setup>
import { ref, computed } from "vue";

const grouping = 3;
const rand = ref("");
const showRand = ref(true);
const showResult = ref(false);
const isCorrect = ref(false);

const digits = ref(null);
const seconds = ref(null);
const memorizedNum = ref(null);
const isFormatted = ref(true);

let timePercentage = ref(0);
let intervalId = null;

const check = () => {
  showResult.value = true;
  if (memorizedNum.value?.replace(/\s/g, "") == rand.value) {
    isCorrect.value = true;
  } else {
    isCorrect.value = false;
  }
};

const generateRand = async () => {
  if (!digits.value || !/^[0-9]*$/.test(digits.value)) {
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

  intervalId = setInterval(incrementTime, 100);
  function incrementTime() {
    if (timePercentage.value <= 100 && seconds.value) {
      timePercentage.value += 10 / seconds.value;
    } else {
      clearInterval(intervalId);
    }
  }
};

const formatNum = (str) => {
  if (!str || !str?.length) return;
  const initIndex = grouping; // index of first space to add
  let spacedStr = "";

  for (let i = 0; i <= str.length - 1; i += initIndex) {
    spacedStr += str.slice(i, i + initIndex) + " ";
  }
  return spacedStr;
};

const reset = () => {
  rand.value = "";
  digits.value = null;
  seconds.value = null;
  memorizedNum.value = null;
  showRand.value = true;
  timePercentage.value = 0;
  showResult.value = false;
  if (intervalId) {
    clearInterval(intervalId);
  }
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
.prevent-select {
  -webkit-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>
