<template>
  <div class="card" @click="selectCard()">
    <div class="card-face is-front" v-if="visible">
      <img :src="`/images/${value}.png`" :alt="value" />
      <img
        v-if="match"
        src="../../public/images/checkmark.svg"
        class="icon-checkmark"
      />
    </div>
    <div class="card-face is-back" v-else>Back</div>
  </div>
</template>

<script>
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
    const selectCard = () => {
      context.emit("select-card", {
        position: props.position,
        faceValue: props.value,
      });
    };

    return {
      selectCard,
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
}
.card-face {
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 5px;
}
.card-face.is-front {
  background-image: url("/images/card-bg.png");
  color: white;
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
</style>
