<template>
    <div class="quesiton-box-container">
        <b-jumbotron>
            <template #lead>
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">
            <b-list-group>
            <b-list-group-item 
            v-for="(answer, index) in answers" :key="index"
            @click.prevent="selectAnswer(index)"
            :class="answerClass(index)"
            >
            {{ answer }}
            </b-list-group-item>
            </b-list-group>

            <b-button variant="primary" @click="submitAnswer" :disabled="selectedIndex === null || answered">Submit</b-button>
            <b-button variant="success" @click="next">Next</b-button>
        </b-jumbotron>
        
    </div>
</template>

<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function
    },
    data() {
        return{
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },
    computed: {
        answers() {
            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            console.log("computed")
            return answers
        }
    },
    watch: {
        currentQuestion :{
            immediate: true,
            handler(){
                this.selectedIndex = null
                this.correctIndex = null
                this.answered = false
                this.shuffleAnswers()
                console.log("watch")
            }
        }

    },
    methods: {
        selectAnswer(index){
            this.selectedIndex = index
            console.log(this.selectedIndex)
        },
        shuffleAnswers(){
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        submitAnswer(){
            let isCorrect = false;
            
            if(this.selectedIndex == this.correctIndex){
                isCorrect = true
            }
            this.answered = true
            this.increment(isCorrect)
        },
        answerClass(index){
            if(!this.answered && this.selectedIndex === index){
                return 'selected'
            }else if(this.answered && this.correctIndex === index){
                return 'correct'
            }else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                return 'incorrect'
            }

        }

    },
    mounted() {
        this.shuffleAnswers()
        console.log("mounted")
    }

}
</script>

<style scoped>
    .list-group {
        margin-bottom: 15px;
    }
    .list-group-item:hover {
        background-color: #eeeeee;
        cursor: pointer;
    }
    .btn {
        margin: 0 5px;
    }
    .selected {
        background-color: lightblue;
    }

    .correct {
        background-color: lightgreen;
    }

    .incorrect{
        background-color: red;
    }


</style>