<template>
    <div class="container">
        <b-jumbotron>
            <b><p class="border border-dark rounded p-2 shadow-lg"
            :class="
                (percentage<=25) ? 'bg-success' : (percentage>25 && percentage <50) ? 'bg-primary' : (percentage>=50 && percentage<75) ? 'bg-warning' : (percentage>=75) ? 'bg-danger' : ''
            "
            v-html="setGreeting"
            ></p></b>

            <hr class="my-4">

             <!-- Using value -->
            <div v-if="incorrectQuestions" class="m-auto p-auto text-center">
            <b-button v-b-toggle="'collapse-2'" class="m-1" @click="isCollapsed">Take a look at incorrect questions <br>
            <b-icon :icon="collapsedState" variant="dark"></b-icon>
            </b-button>


            <!-- Element to collapse -->
            <b-collapse id="collapse-2">
                <b-card>
                    <div class="m-0 border border-dark p-2 shadow" 
                        v-for="(item,key) in questionNumArray" :key="key" 
                        >
                        <div class="font-weight-bold border border-dark m-3 shadow ">{{item}}. Question <br/>    -Category: <span class="text-primary">{{incorrectQuestions[key].category}}</span> 
                        <br>
                        -Difficulty: <span class="text-info">{{incorrectQuestions[key].difficulty}}</span>
                        </div>

                        <p class="m-0 border border-dark p-2" 
                        v-for="(value,ind) in incorrectQuestions[key].incorrect_answers" :key="ind" 
                        :class="{
                            'incorrect':(ind===incorrectQuestionsIndexes[key])
                            
                            }"
                        v-html="value"
                        >
                        </p>
                        <p class="m-0 border border-dark p-2 correct"                 
                        v-html="incorrectQuestions[key].correct_answer"
                        >
                        </p>
                    </div>
                </b-card>
            </b-collapse>
            </div>

            
                <b-button class="mt-3 shadow-lg" variant="primary" 
                @click="tryAgain"
                >Try again</b-button>
        </b-jumbotron>
    </div>
</template>

// TODO: Categories of incorrect questions,
and setting all the question properties to default values instead of reloading

<script>
export default {
    props:{
        incorrectQuestions:Array,
        questionsLength: Number,
        incorrectQuestionsIndexes:Array,
        incorrectAnswers:Array,

    },
    data(){
        return{
            percentage:0,
            questionNumArray:[],
            collapsedState:'caret-down-square'
        }
    },
    methods: {
        tryAgain(){
            location.reload();
        },
        isCollapsed(){
            if(this.collapsedState==='caret-down-square'){
                this.collapsedState='caret-up-square';
            } else {
                this.collapsedState='caret-down-square'
            }

        },
    },
    computed:{
        setGreeting(){
            if(this.percentage<25){
                return `Success, ${this.percentage}% of your answers are incorrect, you could try again to test your luck!`;
                
            } else if(this.percentage>25 && this.percentage < 50){
                return `Not too bad for a rookie, ${this.percentage}% of your answers are incorrect, try again if you like!`;

            } else if(this.percentage>50 && this.percentage<75){
                return `You could do better, ${this.percentage}% of your answers are incorrect, try again!`;

            } else {
                return `Too bad, ${this.percentage}% of your answers are incorrect, try again!`;
            }
        },

    },
    mounted: function(){
            this.percentage=(this.incorrectQuestions.length / this.questionsLength) * 100;
            for(let i=1;i<=this.incorrectQuestions.length;i++){
                this.questionNumArray.push(i);
            }
    }
}
</script>
<style scoped>
    .correct{
        background-color:springgreen;
    }
    .incorrect{
        background-color:tomato;
    }
</style>