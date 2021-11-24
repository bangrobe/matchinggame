<template>
  <div class="card" @click="selectCard()" :class="flipStyle">
    <div class="card-face is-front" v-if="visible">
      <img :src="`/images/${value}.png`" :alt="value" />
      <img
        v-if="match"
        src="../../public/images/checkmark.svg"
        class="icon-checkmark"
      />
    </div>
    <div class="card-face is-back"></div>
  </div>
</template>

<script>
import { computed } from "vue";
export default {
  props: {
    match: {
      type: Boolean,
      required: true,
      default: false,
    },
    position: {
      type: Number,
      required: true,
    },
    value: {
      type: String,
      required: true,
    },
    visible: {
      type: Boolean,
      required: true,
      default: false,
    },
  },
  setup(props, context) {
    const flipStyle = computed(() => {
      return props.visible ? "is-flipped" : "";
    });
    const selectCard = () => {
      context.emit("select-card", {
        position: props.position,
        faceValue: props.value,
      });
    };

    return {
      selectCard,
      flipStyle,
    };
  },
};
</script>

<style>
.card {
  text-align: center;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.5s ease-in;
  transform-style: preserve-3d;
}
.card-face {
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 5px;
  backface-visibility: hidden;
}
.card-face.is-front {
  background-image: url("/images/card-bg.png");
  color: white;
  transform: rotateY(180deg);
}
.card-face.is-back {
  background-color: blue;
  color: white;
  background-image: url("/images/card-bg-empty.png");
}
.icon-checkmark {
  position: absolute;
  bottom: 5%;
  right: 5%;
}
.card.is-flipped {
  transform: rotateY(180deg);
}
</style>
