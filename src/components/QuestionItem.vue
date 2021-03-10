<template>
  <div>
    <el-form ref="form" :model="form" :disabled="question.disableQuestion">
      <div class="question-container">
        <p class="question-content">{{ question.question }}</p>
        <img v-if="question.pic" :src="question.pic" alt="" height="150px" />
        <el-form-item v-if="!question.option1">
          <el-radio-group v-model="form.answer" style="margin-top: 20px">
            <el-radio label="对"></el-radio>
            <el-radio label="错"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item v-else>
          <el-radio-group v-model="form.answer" class="option-container">
            <el-radio :label="question.option1"></el-radio>
            <el-radio :label="question.option2"></el-radio>
            <el-radio :label="question.option3"></el-radio>
            <el-radio :label="question.option4"></el-radio>
          </el-radio-group>
        </el-form-item>
        <div v-show="question.showExplain">
          <p v-if="question.answer === form.answer || form.answer.split('')[0] === question.answer" class="question-content" style="color: #67C23A">
            <i class="el-icon-check"></i>
            答案解析: {{ question.explain }}
          </p>
          <p v-else class="question-content" style="color: #F56C6C">
            <i class="el-icon-close"></i>
            答案解析: {{ question.explain }}
          </p>
          <p class="question-content" style="color: #409eff">
            题目类型: {{ question.chapter }}
          </p>
        </div>
      </div>
    </el-form>
  </div>
</template>

<script>
import { Form, FormItem, Radio, RadioGroup, } from "element-ui";
export default {
  name: "QuestionItem",
  components: {
    ElForm: Form,
    ElFormItem: FormItem,
    ElRadio: Radio,
    ElRadioGroup: RadioGroup,
  },
  props: {
    question: {
      type: Object,
      default: () => {},
    },
  },
  data() {
    return {
      form: {
        answer: "",
      },
    };
  },
  watch: {
    form: {
      handler: function () {
        this.$emit("change-form", this.form.answer);
      },
      immediate: true,
      deep: true,
    },
  },
};
</script>

<style scoped>
.question-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  padding-left: 20px;
}

.option-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: space-between;
  height: 100px;
  margin-top: 20px;
}

.question-content {
  text-align: left;
}
</style>