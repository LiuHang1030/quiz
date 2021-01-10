<template>
  <div>
    <Menu v-if="menu" @playClick="onPlayClick" />
    <Loading v-if="loading" @loaded="onLoaded" @error="onLoadError" />
    <Quiz
      v-if="quiz && quizList"
      :quizList="quizList"
      @quiz="onQuiz"
      @Finished="onFinished"
    />
    <Finished v-if="finished" />
  </div>
</template>


<script>
import StateMachine from "javascript-state-machine";
import Menu from "components/Menu.vue";
import Loading from "components/Loading.vue";
import Quiz from "components/Quiz.vue";
import Finished from "components/Finished.vue";

export default {
  data() {
    return {
      menu: true,
      loading: false,
      quiz: false,
      finished: false,
      quizList: {},
    };
  },
  components: {
    Menu,
    Loading,
    Quiz,
    Finished,
  },
  created() {
    const that = this;
    this.pathFSM = new StateMachine({
      init: "menu",
      transitions: [
        {
          name: "play",
          from: "menu",
          to: "loading",
        },
        {
          name: "loaded",
          from: "loading",
          to: "loaded",
        },
        {
          name: "quiz",
          from: "loaded",
          to: "quizzing",
        },
        {
          name: "finished",
          from: "quizzing",
          to: "end",
        },
      ],
      methods: {
        onPlay() {
          that.menu = false;
          that.loading = true;
          console.log("onplay");
        },
        onLoaded() {
          that.loading = false;
          that.quiz = true;
          console.log("loaded state");
        },
        onQuiz() {
          console.log("开始答题");
        },
        onFinished() {
          that.quiz = false;
          that.finished = true;
        },
      },
    });
  },
  methods: {
    onPlayClick() {
      this.pathFSM.play();
    },
    onLoaded(data) {
      this.quizList = data;
      this.pathFSM.loaded();
    },
    onLoadError() {},
    onQuiz() {
      this.pathFSM.quiz();
    },
    onFinished() {
      if (this.pathFSM.can("finished")) {
        this.pathFSM.finished();
      }
    },
  },
};
</script>

<style>
</style>