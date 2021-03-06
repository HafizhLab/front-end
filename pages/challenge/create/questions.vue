<template>
  <div class="pt-3 pb-5 container">
    <div class="title-sm pb-3">
      <h5>
        <nuxt-link to="/challenge/create">
          <b-icon icon="chevron-left" class="mr-2"></b-icon>
        </nuxt-link>
        {{ name }}
      </h5>
      <h5>
        Create
        <b-icon
          icon="chevron-right"
          class="ml-2"
          @click="createChallenge()"
        ></b-icon>
      </h5>
    </div>
    <h5 style="margin-left: 2.5rem">By {{ type }} : {{ chosen }}</h5>
    <div class="questions-label mt-5">
      <h3 class="text-center">
        <b-icon
          v-if="questionNumber > 1"
          icon="chevron-left"
          class="mr-2"
          @click="changeQuestion(questionNumber - 1)"
        ></b-icon>
        Question {{ questionNumber }}
        <b-icon
          icon="chevron-right"
          class="ml-2"
          @click="changeQuestion(questionNumber + 1)"
        ></b-icon>
      </h3>
    </div>
    <div class="main mt-3 pt-4 pb-3 container">
      <div v-if="currentQuestion" class="question-section py-3 font-kitab">
        <b-icon icon="pencil-square" @click="showModalQuestions()"></b-icon>
        <span>{{ currentQuestion.text }}</span>
        <p class="text-right">Generate Options</p>
      </div>
      <div v-else class="question-section text-center py-5">
        <h5 class="font-weight-bold" @click="showModalQuestions()">
          Select Question Verse
        </h5>
      </div>
      <div class="answer-section">
        <b-button
          v-for="index in 4"
          :key="index"
          class="ans-option-btn"
          variant="outline-info"
          @click="chooseAnswer(index)"
        >
          <div class="row">
            <div class="col-2 icon-ans">
              <b-icon icon="pencil-square" @click="showModalOptions(index)">
              </b-icon>
            </div>
            <div ref="option-text" class="col-10 text-ans no-option">
              Edit option
            </div>
          </div>
        </b-button>
      </div>
    </div>
    <b-modal
      ref="select-questions"
      height="100"
      hide-footer="true"
      title="Choose question"
    >
      <ul>
        <li v-for="item in data" :key="item.name" @click="selectQuestion(item)">
          ({{ item.number }}) {{ item.text }}
        </li>
      </ul>
    </b-modal>
    <b-modal
      ref="select-options"
      height="100"
      hide-footer="true"
      title="Choose options"
    >
      <ul>
        <li v-for="item in data" :key="item.name" @click="selectOptions(item)">
          ({{ item.number }}) {{ item.text }}
        </li>
      </ul>
    </b-modal>
  </div>
</template>

<script>
import Dummy from "~/assets/AlBaqarah.json";

export default {
  data() {
    return {
      name: "",
      type: "",
      basedOn: "",
      chosen: "",
      questionNumber: 1,
      optionId: 1,
      currentQuestion: null,
      currentOptions: [null, null, null, null],
      correctOption: null,
      data: null,
      questions: [
        {
          id: 0,
          question: null,
          options: [null, null, null, null],
        },
      ],
    };
  },
  created() {
    this.data = Dummy.data[0].ayahs;
    this.chosen = this.$route.params.chosen;
    this.name = this.$route.params.name;
    this.type = this.$route.params.type;
    this.basedOn = this.$route.params.basedOn;
  },
  methods: {
    showModalQuestions() {
      this.$refs["select-questions"].show();
    },
    showModalOptions(index) {
      this.optionId = index - 1;
      this.$refs["select-options"].show();
    },
    check(i) {
      this.$refs["option-text"][i].parentNode.parentNode.classList.add(
        "choosen-ans"
      );
      console.log(this.$el);
    },
    uncheck(i) {
      this.$refs["option-text"][i].parentNode.parentNode.classList.remove(
        "choosen-ans"
      );
    },
    selectQuestion(item) {
      this.currentQuestion = item;
      this.questions[this.questionNumber - 1].question = item;
      this.$refs["select-questions"].hide();
    },
    selectOptions(item) {
      var option = { ...item };
      this.currentOptions[this.optionId] = option;
      this.questions[this.questionNumber - 1].options[this.optionId] = option;
      this.questions[this.questionNumber - 1].options[this.optionId][
        "isCorrect"
      ] = false;
      this.$refs["option-text"][this.optionId].innerText = option.text;
      this.$refs["option-text"][this.optionId].classList.add("font-kitab");
      this.$refs["select-options"].hide();
    },
    chooseAnswer(index) {
      var optionId = index - 1;
      if (this.currentOptions[optionId]) {
        if (this.correctOption != null) {
          this.questions[this.questionNumber - 1].options[
            this.correctOption
          ].isCorrect = false;
          this.uncheck(this.correctOption);
        }

        this.questions[this.questionNumber - 1].options[
          optionId
        ].isCorrect = true;
        this.check(optionId);
        this.correctOption = optionId;
      } else {
        this.showModalOptions(index);
      }
    },
    changeQuestion(questionNumber) {
      var question_id = questionNumber - 1;
      this.questionNumber = questionNumber;
      if (question_id >= 0) {
        if (this.questions.length <= question_id) {
          this.questions[question_id] = {
            id: question_id,
            question: null,
            options: [null, null, null, null],
          };
        }
        this.currentQuestion = this.questions[question_id].question;
        this.currentOptions = this.questions[question_id].options;
        this.correctOption = null;

        for (var i = 0; i < 4; i++) {
          var option = this.currentOptions[i];
          if (option) {
            if (option.isCorrect) {
              this.check(i);
              this.correctOption = i;
            } else {
              this.uncheck(i);
            }
            this.$refs["option-text"][i].innerText = option.text;
            this.$refs["option-text"][i].classList.add("font-kitab");
          } else {
            this.uncheck(i);
            this.$refs["option-text"][i].innerText = "Edit option";
            this.$refs["option-text"][i].classList.remove("font-kitab");
          }
        }
      }
    },
    createChallenge() {
      this.$store.commit("ADD_CHALLENGE", {
        id: 0,
        name: this.name,
        num_of_questions: this.questionNumber,
      });
      this.$router.push("/challenge");
    },
  },
};
</script>

<style>
.font-kitab {
  font-family: "Kitab";
  font-size: 1.75rem;
  text-align: right !important;
}

.no-option {
  text-align: left;
}

.title-sm {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.title-sm h5 {
  margin-bottom: 0;
  font-weight: 900;
}

.main {
  border: 1px solid #c1c1c1;
  border-radius: 2rem;
}

.ans-option-btn {
  border-radius: 1rem;
  padding: 1rem;
  min-width: 50%;
  line-height: 1.7;
  color: black;
  align-items: center;
  cursor: pointer;
  margin-bottom: 5px;
  text-align: right;
  background-color: white;
  border: 1px solid#C1C1C1;
  width: 100%;
}

.ans-option-btn:active,
.ans-option-btn:focus {
  outline: none !important;
  box-shadow: none;
}

.ans-option-btn:hover {
  background-color: #49c0db;
  border: 1px solid #49c0db;
}

.answer-section {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.answer-section .b-icon {
  margin-left: 10px;
}

.question-section {
  line-height: 1.7;
  text-align: right;
}

.question-section p {
  font-size: 8pt;
  font-weight: 700;
  margin-top: 10px;
  margin-bottom: 0;
}

.question-section p:hover,
.question-section h5:hover,
.question-section .b-icon:hover,
.questions-label .b-icon:hover {
  cursor: pointer;
  color: #848484;
}

.icon-ans {
  display: flex;
  text-align: left;
  font-size: 1.2rem;
  align-items: center;
}

.choosen-ans {
  border-radius: 1rem;
  padding: 1rem;
  min-width: 50%;
  line-height: 1.7;
  color: black;
  align-items: center;
  cursor: pointer;
  margin-bottom: 5px;
  text-align: right;
  background-color: #f0f9ee;
  border: 1px solid #6ac259;
  width: 100%;
  color: #6ac259;
}

.choosen-ans:hover {
  background: #6ac259;
  border: 1px solid #6ac259;
}

.modal-open {
  padding-right: 0px !important;
}

.modal-open .modal-dialog {
  padding: 5%;
  border-radius: 50px;
  height: 100%;
  margin: auto;
  display: flex;
}

.modal-content {
  margin: auto;
  min-height: fit-content;
  max-height: 80%;
  border-radius: 15px;
}

.modal-body {
  overflow-y: auto;
}

.modal-body ul {
  list-style: none;
  padding: 0;
}

.modal-body li {
  font-size: 13pt;
  text-align: right;
  font-family: "Kitab";
  padding: 0.75rem 0.75rem;
  border-bottom: 1px solid #ededeb;
  cursor: pointer;
}

.modal-body li:hover {
  color: white;
  background: #49c0db;
}
</style>
