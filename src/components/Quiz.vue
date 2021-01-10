<template>
  <div class="quiz-page">
    <div class="time-out">
      {{ count }}
    </div>
    <div class="progress">
      {{ nowIndex + 1 }} /{{ this.quizList.question.length }}
    </div>
    <ul v-for="(quiz, index) in this.quizList.question" :key="index">
      <li v-if="nowIndex == index">
        <h1 class="quiz-title">{{ quiz.title }}</h1>
        <ul class="quiz-options">
          <li
            v-for="(option, subIndex) in quiz.options"
            :key="subIndex"
            class="quiz-options"
            @click="optionClickHandle(quiz, option, index)"
          >
            {{ option.name }}
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<script>
import StateMachine from "javascript-state-machine";
export default {
  props: {
    quizList: {
      defalut() {
        return {};
      },
      type: Object,
    },
  },
  data() {
    return {
      count: 3,
      nowIndex: 0,
      quizFSM: null,
      timer: null,
    };
  },
  created() {
    console.log(this.quizList);
    this.emitQuiz();
    this.initStateMachine(this.quizList);
  },
  methods: {
    initStateMachine(quizList) {
      let that = this;
      let quizTransitions = quizList.question.map((quiz, index) => {
        let isLastOne = index == quizList.question.length - 1;
        let nowIndex = `${index + 1}`;
        let nextIndex = isLastOne ? "done" : `${Number(nowIndex) + 1}`;
        return {
          name: `${isLastOne ? "done" : "next"}`,
          from: nowIndex,
          to: nextIndex,
        };
      });
      console.log(quizTransitions);
      this.quizFSM = new StateMachine({
        init: "1",
        transitions: quizTransitions,
        methods: {
          onNext(e) {
            console.log("onNext");
            return new Promise((resolve, reject) => {
              resolve();
              console.log("动画时间");
              that.nowIndex++;
            });
          },
          onDone() {
            console.log("onDone");
            that.$emit("Finished");
          },

          onTransition(lifecycle, arg1, arg2) {
            //reset count
            that.count = 3;
            clearInterval(that.timer);
            that.timer = setInterval(() => {
              that.count--;
              if (that.count == 0) {
                clearInterval(that.timer);
                if (this.can("done")) {
                  this.done();
                } else if (this.can("next")) {
                  this.next();
                }
              }
            }, 1000);
          },
        },
      });

      //   this.quizFSM.observe("onNext", function (e) {
      //     console.log(e);
      //   });
    },
    emitQuiz() {
      this.$emit("quiz");
    },
    step() {},
    optionClickHandle(quiz, option, index) {
      let isCorrectOption = option.value == quiz.answer;
      console.log(this.quizFSM.can("next"));
      if (this.quizFSM.is(`${index + 1}`)) {
        if (this.quizFSM.can("done")) {
          this.quizFSM.done();
        } else {
          this.quizFSM.next();
        }
      }
    },
  },
};
</script>

<style>
.quiz-page {
  margin: 0;
}
.quiz-page ul,
.quiz-page li {
  margin: 0;
  padding: 0;
  list-style: none;
}
.quiz-options li {
  margin-top: 10px;
  padding: 20px;
  background: #f7f7f7;
  text-align: center;
}
</style>