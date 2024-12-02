<script setup lang="ts">
import { ref, computed } from "vue";
import { useMainStore } from "../store";

const store = useMainStore();
const showRanking = ref(false);

// Define total steps and current progress index
const totalSteps = computed(() => store.getScoreLevels.length);
const currentStep = computed(() => store.getProgressIndex);
</script>

<template>
  <!-- Ranking Dialog -->
  <el-dialog v-model="showRanking" :title="$t('Ranking')">
    <div class="ranking-dialog">
      <p>{{ $t("RankMSG") }}:</p>
      <ul>
        <li
          v-for="(scoreLevel, index) in store.getScoreLevels"
          :key="`ranking${index}`">
          {{ $t(`rank.${index}`) }} ({{ scoreLevel }})
        </li>
      </ul>
    </div>
  </el-dialog>

  <!-- Step Progress -->
  <div class="row" @click="showRanking = true">
    <div class="rank-level">
      <strong>
        {{ $t(`rank.${store.getProgressIndex}`) }}
      </strong>
    </div>

    <div class="step-progress">
      <div v-for="(step, index) in totalSteps" :key="index" class="step">
        <div
          class="circle"
          :class="{
            first: index === 0,
            active: index <= currentStep,
            completed: index < currentStep,
            last: index === totalSteps - 1  // Apply 'last' class for final step
          }"
        >
          <span v-if="index <= currentStep">{{ index }}</span>
        </div>
        <div v-if="index < totalSteps - 1" class="line" :class="{ active: index < currentStep }"></div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
@import "../assets/styles/_variables";

.row {
  margin-top: 20px;
  margin-bottom: 20px;
  display: flex;
  align-items: center;
}

.rank-level {
  margin-right: 20px;
  display: flex;
  align-items: center;
  white-space: nowrap;
}

.ranking-dialog {
  text-align: left;
}

/* Step Progress Styling */
.step-progress {
  display: flex;
  align-items: center;
  justify-content: center;

  .step {
    display: flex;
    align-items: center;
  }

  /* Circle for each step */
  .circle {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background-color: $bl-grey;
    color: black;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
    transition: background-color 0.3s ease, width 0.3s ease, height 0.3s ease;

    /* Hide the number for unactivated steps */
    span {
      display: none;
    }

    /* First step is always yellow and larger */
    &.first {
      width: 35px;
      height: 35px;
      background-color: #fce303;
    }

    /* Activated steps (including current) */
    &.active {
      width: 35px;
      height: 35px;
      background-color: #fce303;

      span {
        display: block;
      }
    }

    /* Completed steps */
    &.completed {
      background-color: #fce303;

      span {
        display: block;
      }
    }

    /* Final step as rectangle */
    &.last {
      width: 12px; /* Same width as the other steps */
      height: 12px; /* Make it a rectangle */
      border-radius: 0px; /* Remove rounding to make it a perfect rectangle */
      background-color: $bl-grey; /* Same color as other unactivated steps */
      display: flex;
      align-items: center;
      justify-content: center;

      span {
        display: block;
      }
    }
  }

  /* Line connecting the steps */
  .line {
    width: 50px;
    height: 2px;
    background-color: $bl-grey;
    transition: background-color 0.3s ease;

    /* Lines of completed steps */
    &.active {
      background-color: #fce303;
    }
  }
}

html.dark .row strong {
  color: $bl-grey;
}

@media only screen and (max-width: 700px) {
  .row {
    margin: 10px;
  }
}
</style>
