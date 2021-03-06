<template>
    <div class="question-box-container">
        <b-jumbotron>

            <template slot="lead">
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item 
                    v-for="(answer, index) in shuffledAnswers" 
                    v-bind:key="index" 
                    @click.prevent="selectedAnswer(index)"
                    v-bind:class="answerClass(index)"
                >
                        {{ answer }}
                </b-list-group-item>
            </b-list-group>

            <b-button 
                variant="primary"
                v-on:click="submitAnswer"
                v-bind:disabled="selectedIndex === null || answered"
            >
                Submit
            </b-button>
            <b-button 
                @click="next" 
                variant="success"
                :disabled="answered === false"
            >
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
    import _ from 'lodash';
    export default {
        props: {
            currentQuestion: Object,
            next: Function,
            increment: Function
        },
        data () {
            return {
                selectedIndex: null,
                shuffledAnswers: [],
                correctIndex: null,
                answered: false
            }
        },
        computed: {
            answers () {
                let answers = [...this.currentQuestion.incorrect_answers];
                answers.push(this.currentQuestion.correct_answer)
                return answers;
            }
        },
        watch: {
            currentQuestion: {
                immediate: true,
                handler () {
                    this.answered = false;
                    this.selectedIndex = null;
                    this.shuffleAnswers();
                }
            },
            // () {
            //     this.selectedIndex = null;
            //     this.shuffleAnswers();
            // }
        },
        methods: {
            selectedAnswer (index) {
                this.selectedIndex = index;
            },
            shuffleAnswers () {
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
                
                this.shuffledAnswers = _.shuffle(answers);
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
            },
            submitAnswer () {
                let isCorrect = false;

                if(this.selectedIndex === this.correctIndex){
                    isCorrect = true;
                }
                this.answered = true;
                this.increment(isCorrect);
            },
            answerClass (index) {
                let answerClass = '';
                
                if(!this.answered && this.selectedIndex === index){
                    answerClass = 'selected';
                }
                else if(this.answered && this.correctIndex === index){
                    answerClass = 'correct';
                }
                else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                    answerClass = 'incorrect';
                }

                return answerClass;
            }
        },
        // mounted () {
        //     this.shuffleAnswers();
        // }
    }
</script>

<style scoped>
    .list-group {
        margin-bottom: 15px;
    }

    .list-group-item:hover {
        background: #eee;
        cursor: pointer;
    }

    .btn {
        margin: 0 5px;
    }

    .selected {
        background-color: #bda6f5;
    }

    .correct {
        background-color: #35e87a;
    }
    .incorrect {
        background-color: #f02939;
    }
</style>