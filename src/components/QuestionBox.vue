<template>
    <div class="container">
        <b-jumbotron>
            <template #lead>
                <b><p class="border border-dark rounded bg-warning p-2 shadow-lg" v-html="currentQuestion.question"
                ></p></b>
            </template>

            <hr class="my-4">

            <p class="m-0 border border-dark p-2" 
            v-for="(answer, index) in shuffledAnswers" :key="index" 
            @click="selectAnswer(index)"
            :class="{
                'dugme':(selectedIndex!=index),
                'selected':(selectedIndex===index),
                'correct':(submitted && index===correctIndex),
                'incorrect':(index!=correctIndex &&selectedIndex===index &&submitted),
                
                }"
            v-html="answer"
            >
            </p>
            <b-button-group class="mt-3 border rounded border-dark">
                <b-button variant="primary" 
                @click="submitAnswer"
                :disabled="(selectedIndex===null || submitted)"
                >Submit</b-button>
                <b-button 
                variant="success" 
                @click="next" 
                :disabled="submitted===false"
                v-text="nextBtn"
                ></b-button>
            </b-button-group>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash';
export default {
    props:{
        currentQuestion:Object,
        next: Function,
        increment: Function,
        questionsLength: Number,
        questionNum: Number,
        incorrectQuestionsIndexes: Array,
        incorrectAnswers: Array,
    },
    data(){
        return{
            selectedIndex: null,
            shuffledAnswers:[],
            submitted: false,
            correctIndex: null,
        }
    },
    computed: {
        answers(){
            let answers = [...this.currentQuestion.incorrect_answers];
            answers.push(this.currentQuestion.correct_answer);
            answers
            return answers;
        },
        nextBtn(){
            return (this.questionsLength===this.questionNum) ? 'Finish' : 'Next';
        }
    },
    methods:{
        selectAnswer(index){
            if(!this.submitted){
                this.selectedIndex=index;
            }
        },
        shuffleAnswers(){
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers);
            this.correctIndex=this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
                
        },
        submitAnswer(){
            if(!this.submitted){
                let isCorrect = false;
                if(this.selectedIndex===this.correctIndex){
                    isCorrect = true;
                    
                } else {
                    this.incorrectQuestionsIndexes.push(this.selectedIndex);
                    this.incorrectAnswers.push(this.shuffledAnswers[this.selectedIndex]);
                }
                this.increment(isCorrect);
                this.submitted=true;
                return isCorrect;
            }
        }

    },
    watch: {
        currentQuestion: {
            immediate:true,
            handler(){
                this.selectedIndex=null; 
                this.shuffleAnswers();
                this.submitted=false;
            },
        
    },
}
}
</script>
<style scoped>
    .dugme{
        background-color:white;
        color:rgb(77, 64, 64);
        transition-duration: 0.5s;
    }
    .dugme:hover{
        background-color:rgb(77, 64, 64);
        color:white;
        cursor: pointer;

    }
    .selected{
        background-color:cornflowerblue;
    }
    .correct{

        background-color:springgreen;
    }
    .incorrect{
        background-color:tomato;
    }

</style> 
