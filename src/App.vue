<template>
  <h1>Peek a Vue</h1>
  <section class="game-board">
    <Card
      v-for="(card, index) in cardList"
      :key="`$card-${index}`"
      :value="card.value"
      :visible="card.visible"
      :match="card.match"
      @select-card="flipCard"
      :position="card.position"
    />
  </section>
</template>

<script>
import { ref, watch } from "vue";
import Card from "@/components/Card";
export default {
  name: "App",
  components: {
    Card,
  },
  setup() {
    const cardList = ref([]);
    const userSelection = ref([]);
    for (let i = 0; i < 16; i++) {
      cardList.value.push({
        value: i,
        visible: false,
        position: i,
        match: false,
      });
    }
    //Listen from emit event select-card from card component
    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true;
      //Truyền data từ hàm select-card ở component Card vào biến userSelection (là một Array)
      //Nếu giá trị đầu của userSelection đã có thì truyền vào giá trị thứ 2
      //Nếu không truyền vào giá trị thứ nhất của Array userSelection
      if (userSelection.value[0]) {
        userSelection.value[1] = payload;
      } else {
        userSelection.value[0] = payload;
      }
    };

    watch(
      userSelection,
      (currentValue) => {
        if (currentValue.length === 2) {
          //Lay gia tri cua currentValue
          const cardOne = currentValue[0];
          const cardTwo = currentValue[1];

          if (cardOne.faceValue === cardTwo.faceValue) {
            // Nếu faceValue của cardOne bằng với faceValue của cardTwo
            //value cuả position trong cardList tương ứng với postion của cardOne
            //và cardTwo sẽ nhận giá trị match là true

            cardList.value[cardOne.position].match = true;
            cardList.value[cardTwo.position].match = true;
          } else {
            cardList.value[cardOne.position].visible = false;
            cardList.value[cardTwo.position].visible = false;
          }
          //Reset array
          userSelection.value.length = 0;
        }
      },
      //https://v3.vuejs.org/guide/migration/watch.html
      //To trigger array mutation, the deep option must be specific.
      { deep: true }
    );

    return {
      cardList,
      userSelection,
      flipCard,
    };
  },
};
</script>

<style>
.game-board {
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
  justify-content: center;
}
</style>
