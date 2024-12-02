<script setup lang="ts">
import { computed, ref } from "vue";
import { useMainStore } from "../store";
import { gridify } from "../utils";

import { useI18n } from "vue-i18n";
import en from "../locales/en.json";

const { t } = useI18n({
  inheritLocale: true,
  messages: {
    en,
  },
});

const store = useMainStore();
// showWords el-collapse has 2 elems when expanded, 1 when collapsed.
const showWords = ref([false]);
const numCorrectMessage = computed(() => {
  return t("foundWords", store.getCorrectGuesses.length);
});

const lastFiveGuesses = computed(() => {
  const numGuessesToShow = Math.min(store.getCorrectGuesses.length, 5);
  return store.getCorrectGuesses.reverse().slice(0, numGuessesToShow);
});

// Alphabetical when expanded, in order found when collapsed.
const gridData = computed(() => {
  // Arrange data into rows instead of columns
  const data = Array.from(store.getCorrectGuesses).sort();
  // Wrap each word into an object for the table to display it as a row
  return data.map((word) => ({ word }));
});
</script>

<template>
  <el-collapse
    v-model="showWords"
    @change="showWords.length == 2 ? $emit('open') : $emit('close')">
    <el-collapse-item>
      <template #title>
        <template v-if="showWords.length === 2">
          {{ numCorrectMessage }}
        </template>
        <template v-else-if="lastFiveGuesses.length === 0">
          {{ t("Your words") }}...
        </template>
        <template v-else>
          <span
            v-for="(guess, index) in lastFiveGuesses"
            :key="guess"
            :class="store.cellClassName({ row: [guess], columnIndex: -1 })">
            {{ guess }}{{ index === lastFiveGuesses.length - 1 ? "" : ", " }}
          </span>
          <span v-if="lastFiveGuesses.length === 5"> ... </span>
        </template>
      </template>
      <el-table
        :data="gridData"
        class="correct-guesses-table"
        :cell-class-name="store.cellClassName">
        <el-table-column
          prop="word" />
      </el-table>
    </el-collapse-item>
  </el-collapse>
</template>

<style scoped lang="scss">
@import "../assets/styles/_variables";

.correct-guesses-table {
  min-height: 50vh;
}

html.dark .el-collapse {
  background-color: $bl-el-plus-dark-bg;
}

.el-collapse {
  border: 0px;
  font-size: 16px;
}

.el-collapse-item .el-collapse-item__header span,
.el-collapse-item .el-collapse-item__header template {
  font-size: 16px;
  font-weight: 500;
  line-height: 1.5;
}

// If specific text inside el-table should also be adjusted
.correct-guesses-table .el-table__cell {
  font-size: 16px;
}

.el-collapse-item__header.is-active {
  font-size: 16px; /* Change the font size as needed */
  font-weight: 600; /* Optional: Adjust font weight */
  line-height: 1.5;
}
</style>
