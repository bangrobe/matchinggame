<template>
  <h1>Peek a Vue</h1>
  <button @click="shuffleCards">Shuffle Cards</button>
  <button @click="restartGame">Restart Game</button>
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
    <p>{{ status }}</p>
  </section>
</template>

<script>
import _ from "lodash";
import { ref, watch, computed } from "vue";
import Card from "@/components/Card";
export default {
  name: "App",
  components: {
    Card,
  },
  setup() {
    const cardList = ref([]);
    const userSelection = ref([]);

    const cardItems = [1, 2, 3, 4, 5, 6, 7, 8];
    //Thiết lập trong mỗi item thì chèn vào cardList 2 lần
    cardItems.forEach((item) => {
      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        match: false,
      });

      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        match: false,
      });
    });

    //Với mỗi item trong cardList map với index mới
    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index,
      };
    });
    //Lấy số cặp còn lại
    //Kiểm tra các card trong cardList có match = false
    //remainingPairs trả về một array nên phải có thêm length
    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter(
        (card) => card.match === false
      ).length;
      // prettier-ignore
      return (remainingCards/2);
    });

    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return "Player Win";
      } else {
        return `Remaining Pairs: ${remainingPairs.value}`;
      }
    });

    const shuffleCards = () => {
      cardList.value = _.shuffle(cardList.value);
    };

    const restartGame = () => {
      //Shuffle lại array cardList
      shuffleCards();
      // Map các phần tử trong cardList với match, position và visible mới
      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          match: false,
          position: index,
          visible: false,
        };
      });
    };
    //Listen from emit event select-card from card component
    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true;
      //Truyền data từ hàm select-card ở component Card vào biến userSelection (là một Array)
      //Nếu giá trị đầu của userSelection đã có thì truyền vào giá trị thứ 2
      //Nếu không truyền vào giá trị thứ nhất của Array userSelection
      //Fix tile match itself

      if (userSelection.value[0]) {
        //Nếu userSelection position và faceValue bằng với giá trị data truyền vào
        //Thì bỏ qua
        if (
          userSelection.value[0].position === payload.position &&
          userSelection.value[0].faceValue === payload.faceValue
        ) {
          return;
        } else {
          userSelection.value[1] = payload;
        }
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
            setTimeout(() => {
              cardList.value[cardOne.position].visible = false;
              cardList.value[cardTwo.position].visible = false;
            }, 1000);
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
      cardItems,
      userSelection,
      flipCard,
      remainingPairs,
      status,
      shuffleCards,
      restartGame,
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
