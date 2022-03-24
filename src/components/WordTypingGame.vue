<template>
  <header
    style="
      display: flex;
      justify-content: center;
      padding: 0.5rem 0;
      background-color: blue;
    "
  >
    <label for="select-difficulty-level" style="color: white"
      >Select the level of difficulty you want:
    </label>
    <select
      id="select-difficulty-level"
      v-model="selectedDifficulty"
      style="margin-left: 0.5rem"
      @change="handleDifficultyChange"
    >
      <option v-for="(value, key) in difficultyLevels" :key="key" :value="key">
        {{ value.label }}
      </option>
    </select>
  </header>

  <div
    style="
      margin: auto;
      margin-top: 2rem;
      border: 2px solid black;
      width: 60%;
      padding: 1rem;
      max-width: 45rem;
    "
  >
    <div style="display: flex; flex: 1; justify-content: space-between">
      <span>Time Left: {{ timeLeft }}</span>
      <span>Score: {{ score }}</span>
    </div>
    <div
      style="
        display: flex;
        flex-direction: column;
        align-items: center;
        margin: 1rem 0;
      "
    >
      <p>Enter the below word:</p>
      <p>{{ displayedWord }}</p>
      <input
        id="word-entry"
        ref="inputRef"
        v-model="input"
        :class="{ 'margin-bottom-1': msg }"
        autocomplete="off"
        @input="handleInputChange"
      />
      <p v-if="msg">{{ msg }}</p>
    </div>
  </div>

  <!-- <form style='display: flex; flex-direction: column; align-items: center; justify-content: center; margin-top: 30px' @submit.prevent="handleFormSubmission">
    <input v-model="username" id="'username" style='margin-bottom: 1rem' autocomplete='off'/>
    <button type="submit">Submit</button>
  </form>

  <div style="margin-top: 2rem; text-align:center">
    <div v-if="userFound" style="color: green;">{{ userInfo }}</div>
    <p v-if="errorMsg" style="color: red">{{ errorMsg }}</p>
  </div> -->
</template>

<script lang="ts">
import { defineComponent, onMounted, onUnmounted, PropType, ref } from 'vue';

const difficultyLevels = {
  easy: {
    label: 'Begineer',
    time: 10,
  },
  medium: {
    label: 'Intermediate',
    time: 7,
  },
  hard: {
    label: 'Veteran',
    time: 4,
  },
};

const getRandomWord = (wordList: string[]) => {
  return wordList[Math.floor(Math.random() * wordList.length)];
};

export default defineComponent({
  name: 'WordTypingGame',
  props: {
    wordList: {
      type: Array as PropType<string[]>,
      required: true,
    },
  },

  setup(props) {
    const selectedDifficulty = ref<'easy' | 'medium' | 'hard'>('easy');
    const displayedWord = ref('');
    const timeLeft = ref(0);
    const score = ref(0);
    const input = ref<string>('');
    const msg = ref<string>('');
    const gameOver = ref(false);
    const timerStarted = ref(false);
    const timerId = ref<NodeJS.Timeout | null>(null);
    const inputRef = ref<HTMLInputElement | null>(null);
    // const userFound = ref(false);
    // const userInfo = ref({});
    // const username = ref('');
    // const errorMsg = ref('');

    // const handleFormSubmission = () => {
    //   if (username.value !== '1') {
    //     userFound.value = false;
    //     userInfo.value = {};
    //     errorMsg.value = 'Sorry the user was not found';
    //     return;
    //   }
    //   userFound.value = true;
    //   errorMsg.value = '';
    //   userInfo.value = {
    //     id: 1,
    //     name: 'Leanne Graham',
    //     username: 'Bret',
    //     email: 'Sincere@april.biz',
    //     address: {
    //       street: 'Kulas Light',
    //       suite: 'Apt. 556',
    //       city: 'Gwenborough',
    //       zipcode: '92998-3874',
    //       geo: {
    //         lat: '-37.3159',
    //         lng: '81.1496',
    //       },
    //     },
    //     phone: '1-770-736-8031 x56442',
    //     website: 'hildegard.org',
    //     company: {
    //       name: 'Romaguera-Crona',
    //       catchPhrase: 'Multi-layered client-server neural-net',
    //       bs: 'harness real-time e-markets',
    //     },
    //   };
    // };

    const getDifficultyLevelTime = () => {
      return difficultyLevels[selectedDifficulty.value].time;
    };

    const nextWord = () => {
      displayedWord.value = getRandomWord(props.wordList as string[]);
      timeLeft.value = getDifficultyLevelTime();
      input.value = '';
      inputRef.value?.focus();
    };

    const startTimer = () => {
      timeLeft.value = getDifficultyLevelTime();
      timerStarted.value = true;

      if (timerId.value) {
        clearInterval(timerId.value);
        timerId.value = null;
      }

      timerId.value = setInterval(() => {
        if (timeLeft.value > 0) {
          timeLeft.value--;
        } else if (timeLeft.value === 0) {
          gameOver.value = true;
          clearInterval(timerId?.value as NodeJS.Timeout);
          timerStarted.value = false;
          msg.value = `Timeout!!! Game over! Your final score is ${score.value}`;
        } else {
          timerStarted.value = false;
          msg.value =
            'Some error occured!!! Please restart the game by reloading the page';
        }
      }, 1000);
    };

    const handleDifficultyChange = () => {
      nextWord();
      if (gameOver.value) {
        msg.value = '';
        score.value = 0;
        startTimer();
        gameOver.value = false;
      }
    };

    const handleInputChange = () => {
      if (!gameOver.value) {
        if (!timerStarted.value) {
          startTimer();
        }
        msg.value = '';
      }

      if (input.value === displayedWord.value) {
        nextWord();
        msg.value = 'Correct';
        if (!gameOver.value) {
          score.value++;
        } else {
          gameOver.value = false;
          score.value = 0;
          msg.value = '';
          startTimer();
        }
      }
    };

    onMounted(() => {
      displayedWord.value = getRandomWord(props.wordList as string[]);
      timeLeft.value = getDifficultyLevelTime();
    });

    onUnmounted(() => {
      clearTimeout(timerId.value as NodeJS.Timeout);
    });

    return {
      difficultyLevels,
      selectedDifficulty,
      displayedWord,
      timeLeft,
      score,
      input,
      msg,
      inputRef,
      timerStarted,
      handleInputChange,
      handleDifficultyChange,
      // handleFormSubmission,
      // userInfo,
      // userFound,
      // username,
      // errorMsg,
    };
  },
});
</script>

<style scoped lang="scss">
.margin-bottom-1 {
  margin-bottom: 1rem;
}
</style>
