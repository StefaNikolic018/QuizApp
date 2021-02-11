<template>
  <div id="app">
    <b-container class="bv-example-row">
  <b-row>
    <b-col sm="6" offset="3">
      <Header
        :numCorrect="numCorrect"
        :numTotal="numTotal"
        />
      <QuestionBox 
        v-if="questions.length && !finished"
        v-bind:currentQuestion="questions[index]"
        v-bind:next="next"
        :increment="increment"
        :questionsLength="questionsLength"
        :questionNum="questionNum"
        :incorrectQuestionsIndexes="incorrectQuestionsIndexes"
        :incorrectAnswers="incorrectAnswers"
      />
      <Finished 
        v-else-if="finished"
        :incorrectQuestions="incorrectQuestions"
        :questionsLength="questionsLength"
        :incorrectQuestionsIndexes="incorrectQuestionsIndexes"
        :incorrectAnswers="incorrectAnswers"
      />


    </b-col>
  </b-row>
</b-container>


  </div>
</template>

<script>
import Header from './components/Header.vue'
import QuestionBox from './components/QuestionBox.vue'
import Finished from './components/Finished.vue'



export default {
  name: 'App',
  components: {
    Header,
    QuestionBox,
    Finished,
  },
  data(){
    return {
      questions:[],
      index:0,
      numCorrect:0,
      numTotal:0,
      finished: false,
      numIncorrect:0,
      incorrectQuestions:[],
      incorrectQuestionsIndexes:[],
      incorrectAnswers:[],
      questionNum:1,

    }
  },
  methods:{
    next(){
      if(this.questionNum<this.questionsLength){
        this.questionNum++;
      } else {
        this.finished=true;
      }
      if(this.index!==this.questionsLength){
        this.index++;
      }
    },
    increment(isCorrect){
      if(isCorrect){
        this.numCorrect++;
      } else {
        this.incorrectQuestions.push(this.questions[this.index]);
      }
      this.numTotal++;
    }
  },
  computed:{
    questionsLength(){
      return this.questions.length;
    }
  },
  mounted: function(){
    fetch('https://opentdb.com/api.php?amount=10',{
      method:'get'
    }).then((res)=>{
      return res.json();
    }).then((jsonData)=>{
        this.questions=jsonData.results;
    });
  },
  // watch: {
  //   numTotal:{
  //     immediate:true,
  //     handler(){
  //       if(this.numTotal===this.questions.length){
  //         this.finished=true;
  //       }
  //     }
  //   }
  // }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
