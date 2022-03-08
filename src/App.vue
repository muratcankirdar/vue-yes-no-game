<script>
import axios from 'axios';
import { debounce } from 'lodash';

export default {
  name: 'App',
  components: {},
  data() {
    return {
      question: '',
      answer: '',
      image: null,
      getDebouncedAnswer: null,
    };
  },
  watch: {
    question() {
      this.getDebouncedAnswer();
    },
  },
  created() {
    this.getDebouncedAnswer = debounce(this.getAnswer, 500);
  },
  methods: {
    getAnswer() {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually ends with a question mark. 😊';
        this.image = null;

        return;
      }

      this.answer = 'Thinking...';

      axios.get('https://yesno.wtf/api')
        .then(({ data }) => {
          const {
            answer = '',
            image = '',
          } = data;

          this.answer = answer;
          this.image = image;
        })
        .catch(() => {
          this.answer = 'Error while trying to get response';
          this.image = '';
        });
    },
  },
};
</script>

<template>
  <div id="app">
    <h2>Yes No Game</h2>

    <label for="question">
      <input
        id="question"
        v-model="question"
        type="text"
        name="question"
        placeholder="Type a yes/no question"
      >
    </label>

    <p>{{ answer }}</p>

    <img id="answer" v-if="image" :src="image" alt="Answer image">
  </div>
</template>

<style>
#app {
  font-family: Roboto, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 3em;
}

#question {
  width: 200px;
}

#answer {
  max-width: 100%;
}
</style>
