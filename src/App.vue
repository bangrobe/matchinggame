<template>
  <img src="/images/peek-a-vue-title.png" alt="Peek a vue" />

  <transition-group tag="section" class="game-board" name="shuffle-cards">
    <Card
      v-for="card in cardList"
      :key="`${card.value} - ${card.variant}`"
      :value="card.value"
      :visible="card.visible"
      :match="card.match"
      @select-card="flipCard"
      :position="card.position"
    />
  </transition-group>
  <div>
    <p>{{ status }}</p>
    <button @click="restartGame" class="button">Restart Game</button>
  </div>
</template>

<script>
/*
Shuffle Cards Animation
Vue hỗ trợ một kỹ thuật gọi là transition-group
https://v3.vuejs.org/guide/transitions-enterleave.html#transition-classes
Phải thay đổi :key="index" sang :key="${card-value} - ${card-variant}" vì khi shuffle thì index vẫn không đổi, nên transition không thực hiện được

*/
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

    const cardItems = [
      "bat",
      "candy",
      "cauldron",
      "cupcake",
      "ghost",
      "moon",
      "pumpkin",
      "witch-hat",
    ];
    //Thiết lập trong mỗi item thì chèn vào cardList 2 lần
    cardItems.forEach((item) => {
      cardList.value.push({
        value: item,
        variant: 1,
        visible: false,
        position: null,
        match: false,
      });

      cardList.value.push({
        value: item,
        variant: 2,
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
html,
body {
  margin: 0;
  padding: 0;
}
#app {
  font-family: Arial, Helvetica, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: antialiased;
  text-align: center;
  color: #2c3e50;
  background-image: url("../public/images/page-bg.png");
  background-color: #00070c;
  height: 100vh;
  color: white;
  padding-top: 60px;
}

h1 {
  margin-top: 0;
}
.game-board {
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-template-rows: repeat(4, 120px);
  grid-column-gap: 24px;
  grid-row-gap: 24px;
  justify-content: center;
}
.button {
  padding: 10px 20px;
  background-color: orange;
  border-radius: 4px;
  color: white;
  cursor: pointer;
  border: none;
}
.button:hover {
  background-color: orangered;
  transition: all 0.5s;
}

.shuffle-cards-move {
  transition: transform 0.8s ease-in;
}
</style>
