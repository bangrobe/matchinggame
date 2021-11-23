<template>
  <div class="card" @click="selectCard()">
    <div class="card-face is-front" v-if="visible">
      {{ value }} - {{ match }}
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
      type: Number,
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
  border: 5px solid #ccc;
  text-align: center;
  position: relative;
}
.card-face {
  width: 100%;
  height: 100%;
  position: absolute;
}
.card-face.is-front {
  background-color: red;
  color: white;
}
.card-face.is-back {
  background-color: blue;
  color: white;
}
</style>
