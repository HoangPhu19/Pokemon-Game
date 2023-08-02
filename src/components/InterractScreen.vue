<template>
  <div class="screen">
    <div
      class="screen-inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-flip
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index, value: card }"
        @onFlip="onClickCard($event)"
        :cardsContext="cardsContext"
      />
    </div>
  </div>
</template>

<script>
import CardFlip from "./Card-game.vue";

export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardFlip,
  },
  data: () => {
    return {
      rules: [],
    };
  },
  methods: {
    onClickCard(card) {
      this.rules.push(card);

      console.log(this.rules);

      if (this.rules[0]?.index === this.rules[1]?.index) {
        this.rules.pop();
        return;
      }

      if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        setTimeout(() => {
          // close two card
          this.$refs[`card-${this.rules[0]?.index}`]?.[0]?.onFlipBackCard();
          this.$refs[`card-${this.rules[1]?.index}`]?.[0]?.onFlipBackCard();

          // reset rules to [];
          this.rules = [];
        }, 800);
      }

      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        console.log("right...");

        //add class 'disabled' to components card
        this.$refs[`card-${this.rules[0].index}`][0].onEnableDisableMode();
        this.$refs[`card-${this.rules[1].index}`][0].onEnableDisableMode();

        // reset rules to []
        this.rules = [];

        const disabledElement = document.querySelectorAll(
          ".screen .card.disabled"
        );

        if (
          disabledElement &&
          disabledElement.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 2000);
        }
        return;
      }

      return false;
    },
  },
};
</script>

<style scoped>
.screen {
  width: 100vw;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}
.screen-inner {
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
