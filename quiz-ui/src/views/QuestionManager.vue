<template>
  <div class="container">
    <div class="row main-container justify-content-center">
      <div class="col-8">
        <div class="row text-center">
          <div class="col-6">
            <span class="badge primary-badge main-title question-title-badge">
              Q° {{ currentQuestionPosition }} / {{ totalNumberOfQuestion }}
            </span>
          </div>
          <div class="col-6">
            <span class="badge primary-badge main-title question-title-badge">
              {{ username }}
            </span>
          </div>
        </div>
        <div class="card shadow border-0">
          <div class="card-body">
            <div class="offset-md-2 col-md-8 col-12" style="height: 100%; padding-top: 2rem">
              <div class="row text-center">
                <QuestionDisplay
                  :question="currentQuestion"
                  @answer-selected="answerClickedHandler"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import participationStorageService from '../services/ParticipationStorageService'
import quizApiService from '../services/QuizApiService'
import QuestionDisplay from '../components/QuestionDisplay.vue'

export default {
  name: 'QuestionManager',
  data() {
    return {
      currentQuestion: {
        questionTitle: '',
        questionText: '',
        questionImage: '',
        possibleAnswers: []
      },
      currentQuestionPosition: 1,
      selectedAnswers: [],
      username: '',
      totalNumberOfQuestion: 0
    }
  },
  components: {
    QuestionDisplay
  },
  async created() {
    const quizInfo = await quizApiService.getQuizInfo()
    this.username = participationStorageService.getPlayerName()
    this.totalNumberOfQuestion = quizInfo.data.size
    this.loadQuestionByPosition()
  },
  methods: {
    async loadQuestionByPosition() {
      let data = await quizApiService.getQuestionByPosition(this.currentQuestionPosition)
      const question = data.data

      this.currentQuestion.questionTitle = question.title
      this.currentQuestion.questionText = question.text
      this.currentQuestion.questionImage = question.image
      this.currentQuestion.possibleAnswers = question.possibleAnswers
    },
    async answerClickedHandler(answerId) {
      this.selectedAnswers.push(answerId)
      if (this.currentQuestionPosition === this.totalNumberOfQuestion) {
        this.endQuiz()
      } else {
        this.currentQuestionPosition += 1
        await this.loadQuestionByPosition()
      }
    },
    async endQuiz() {
      this.username = participationStorageService.getPlayerName()
      let data = await quizApiService.postAnswers(this.username, this.selectedAnswers)
      participationStorageService.saveParticipationScore(data.data.score)
      this.$router.push('/end-quiz')
    }
  }
}
</script>
