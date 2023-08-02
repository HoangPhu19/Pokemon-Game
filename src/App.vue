<template>
  <main-screen
    v-if="statusMatch === 'default'"
    @onStart="onHandleBeforeStart($event)"
  />
  <interract-screen
    v-if="statusMatch === 'match'"
    :cardsContext="settings.cardsContext"
    @onFinish="onGetResult"
  />
  <result-screen
    :timer="timer"
    v-if="statusMatch === 'result'"
    @onStartAgain="statusMatch = 'default'"
  />
  <copy-right-screen />
</template>

<script>
import MainScreen from "./components/MainScreen.vue";
import InterractScreen from "./components/InterractScreen.vue";
import ResultScreen from "./components/ResultScreen.vue";
import CopyRightScreen from "./components/CopyRightScreen.vue";

import { shuffled } from "./assets/utils";

export default {
  name: "App",
  components: {
    MainScreen,
    InterractScreen,
    ResultScreen,
    CopyRightScreen,
  },
  data() {
    return {
      settings: {
        totalOfBlocks: 0,
        cardsContext: [],
        startedAt: null,
      },
      statusMatch: "default",
      timer: 0,
    };
  },
  methods: {
    onHandleBeforeStart(config) {
      console.log("Running Here...", config);
      this.settings.totalOfBlocks = config.totalOfBlocks;
      const firstCards = Array.from(
        { length: this.settings.totalOfBlocks / 2 },
        (_, i) => i + 1
      );
      const secondCards = [...firstCards];
      const Cards = [...firstCards, ...secondCards];
      this.settings.cardsContext = shuffled(Cards);
      this.settings.startedAt = new Date().getTime();

      //data ready
      this.statusMatch = "match";
    },
    onGetResult() {
      // get timer
      this.timer = new Date().getTime() - this.settings.startedAt;

      // switch to Result Components
      this.statusMatch = "result";
    },
  },
};
</script>
