<template>
  <div>
    <b-jumbotron>
      <template #lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button 
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered">Submit</b-button>
      <b-button @click="next" variant="success" href="#">Next</b-button>
    </b-jumbotron>
  </div>
</template>
<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  // To shuffle the question in every single changes
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
    /*(){
      this.selectedIndex = null
      this.shuffleAnswers()
    }*/
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submitAnswer(){
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex){
        isCorrect = true;
      }
      this.answered = true
      this.increment(isCorrect)
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass(index){
      let answerClass = ''
      if(!this.answered && this.selectedIndex === index){
        answerClass = 'selected'
      } else if (this.answered && this.correctIndex === index){
        answerClass = 'correct'
      } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = 'incorrect'
      }
      return answerClass
    /*[!answered && selectedIndex === index ? 'selected' : 
          answered && correctIndex === index ? 'correct' : 
          answered && selectedIndex === index && correctIndex !== index ? 'incorrect' : ''
          ]*/
    }
  },
};
</script>
<style scoped>
.list-group {
  margin-bottom: 1rem;
}
.list-group-item {
  transition: background ease 0.3s;
}
.list-group-item:hover {
  background: #f5f5f5;
  color: #343a40;
  cursor: pointer;
}
.btn {
  margin: 0 0.5rem;
}

.selected {
  background-color: #00bffe;
  color: #fff;
}
.selected:hover {
  background-color: #009ed3;
  color: #fff;
}
.correct {
  background-color: #28a745;
  color: #fff;
}
.correct:hover{
  background-color: #4ac466;
  color: #fff;
}
.incorrect {
  background-color: #dc3545;
  color: #fff;
}
.incorrect:hover {
  background-color: #ed495a;
  color: #fff;

}
</style>