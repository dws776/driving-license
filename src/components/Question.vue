<template>
  <div>
    <el-container>
      <el-main>
        <el-card class="box-card" v-loading="loading">
          <div
            v-for="question in questionQueue"
            :key="question.question + question.answer"
            class="question-box"
          >
            <QuestionItem
              :question="question ? question : {}"
              @change-form="questionDidFinish"
            />
          </div>
          <el-button
            v-if="questionQueue.length > 0 && !showEnd"
            type="primary"
            @click="fetchNext()"
            >下一题</el-button
          >
          <p v-if="showEnd">您已完成所有题目！</p>
        </el-card>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import QuestionItem from "./QuestionItem";
import { Container, Main, Card, Button } from "element-ui";
import axios from "axios";

export default {
  name: "Question",
  components: {
    QuestionItem,
    ElContainer: Container,
    ElMain: Main,
    ElCard: Card,
    ElButton: Button,
  },
  data() {
    return {
      loading: false,
      isNext: true,
      questionQueue: [],
      showEnd: false,
    };
  },
  mounted() {
    this.loading = true;
    axios.get("/next").then((res) => {
      console.log(res.data);
      res.data.data.showExplain = false;
      res.data.data.disableQuestion = false;
      this.questionQueue.push(res.data.data);
      this.loading = false;
    });
  },
  methods: {
    async fetchNext() {
      if (!this.isNext) {
        this.$notify.error({
          title: "Error",
          message: "请先完成当前题目",
          duration: 3000,
          offset: 100,
        });
        return;
      }
      const qid = this.questionQueue[this.questionQueue.length - 1].qid;
      this.loading = true;
      await axios.post(`/answered/${qid}`);
      await axios.get("/next").then((res) => {
        console.log(res.data, "next");
        this.questionQueue[this.questionQueue.length - 1].showExplain = true;
        this.questionQueue[
          this.questionQueue.length - 1
        ].disableQuestion = true;
        if (res.data.code === 1) {
          this.showEnd = true;
          this.setCookie("answered", "", -1);
          return;
        }
        res.data.data.showExplain = false;
        res.data.data.disableQuestion = false;
        this.questionQueue.push(res.data.data);
      });
      this.loading = false;
    },
    questionDidFinish(answer) {
      if (!answer) {
        this.isNext = false;
      } else {
        this.isNext = true;
      }
    },
    setCookie(cname, cvalue, exdays) {
      const d = new Date();
      d.setTime(d.getTime() + exdays * 24 * 60 * 60 * 1000);
      const expires = "expires=" + d.toUTCString();
      document.cookie = cname + "=" + cvalue + "; " + expires;
    },
  },
};
</script>

<style scoped>
.box-card {
  width: 900px;
  margin: auto;
}

.question-box {
  margin-bottom: 20px;
  border: 2px solid #409eff;
  border-radius: 10px;
}
</style>