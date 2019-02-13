<template>
  <div id="app" class="app-verbs text-center">
    <div class="container">
      <h1>Irregular verbs</h1>
      <hr>
      <div class="app-verbs__start-block" v-if="showStartBlock">
        <div class="app-verbs__level-block">
          <LevelButton v-bind:levels="levels"
                       v-bind:activeLevelObject="activeLevelObject"
                       v-bind:curLevelObject="level"
                       v-for="level in levels"
                       v-on:on-change-level="toChangeLevel"
                       >
          </LevelButton>
        </div>
        <div class="alert alert-light" role="alert">
          {{  levelDescripton }}
        </div>
        <br>
        <button class="btn btn-outline-light btn-lg"
                v-on:click="toStartTest"
                >
                To start test
        </button>
      </div>
       <TestItem v-if="showTestItem"
                 v-bind:workingArray="workingVerbsArray"
                 v-bind:activeLevelObject=" activeLevelObject"
                 v-on:on-result="toResultTest"
                 >
       </TestItem>
       <div v-if="showResult">
         <h2>Your result:</h2>
         <hr>
         <p>Total questions: <b>{{totalQuestions}}</b></p>
         <p>Amount right answers: <b>{{amountRightAnswers}}</b> / <span>{{(amountRightAnswers*100)/totalQuestions}}%</span></p>
         <p>Amount wrong answers: <b>{{amountWrongAnswers}}</b> / <span>{{100-(amountRightAnswers*100/totalQuestions)}}%</span></p>
         <p>Grade: <b>{{grade}}</b></p>
         <br>
         <br>
         <br>
         <button type="button" class="btn btn-light btn-lg" v-on:click="toRestartTest">Restart test</button>
       </div>

    </div>
  </div>
</template>

<script>
import vars from './vars'
import LevelButton from './components/LevelButton.vue'
import TestItem from './components/TestItem.vue'

export default {
  name: 'app',

  data: function () {
    return {
      levels: vars.levels,
      activeLevelObject: vars.levels[vars.startLevel],
      testStart: false,
      workingVerbsArray: [],
      testResult: false,
      testStatus: 'start',
      resultArr: [],
    }
  },
  components: {
    LevelButton,
    TestItem,
  },
  methods: {
    toChangeLevel(activeLevelObject) {
       this.activeLevelObject = activeLevelObject;
    },
    toStartTest() {
      this.toChangeWorkingArray();
      this.testStatus = 'test';
    },
    toResultTest(answerArray) {
      this.testStatus = 'result';

      for(let i=0; i<answerArray.length; i++) {
        let result = 'success';
        for(let k in answerArray[i]) {
          
          if(answerArray[i][k] === 'error') {
             result = 'error'
          }
        }
        this.resultArr[i] = result;
      }

      console.log(answerArray);
      console.log(this.resultArr)
     
    },
    toMixArray(array) {
      var currentIndex = array.length,
        temporaryValue, randomIndex;
      while (0 !== currentIndex) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex -= 1;
        temporaryValue = array[currentIndex];
        array[currentIndex] = array[randomIndex];
        array[randomIndex] = temporaryValue;
      }

      return array;
    },
    toCropArray(array, amount) {
      let arr = array.splice(0, amount);
      return arr;
    },
    toChangeWorkingArray() {
      let workingArray = [];

      workingArray = this.toMixArray(vars.verbsList);
      workingArray = this.toCropArray(workingArray, +this.activeLevelObject.amount);

      this.workingVerbsArray = [...workingArray].slice();
    },
    toFindVerb() {
      let currentVerbObject = workingVerbsArray[0];
      this.currentVerbObject = currentVerbObject;
    },
    toRestartTest() {
      this.activeLevelObject= vars.levels[vars.startLevel];
      this.testStart= false;
      this.workingVerbsArray= [];
      this.testResult= false;
      this.testStatus= 'start';
      this.resultArr= [];
    }
  },
  computed: {
     levelDescripton() {
      return this.activeLevelObject.descr
     },
     showResult() {
      if(this.testStatus === 'result') {
        return true;
      } else {
        return false;
      }
     },
     showStartBlock() {
      if(this.testStatus === 'start') {
        return true;
      } else {
        return false;
      }
     },
     showTestItem() {
      if(this.testStatus === 'test') {
        return true;
      } else {
        return false;
      }
     },
     amountRightAnswers() {
      let amount = 0;
      for(let item of this.resultArr) {
        if(item==='success') {
          amount++
        }
      }
      return amount;
     },
     totalQuestions() {
      return this.resultArr.length;
     },
     amountWrongAnswers() {
      let amount = 0;
      for(let item of this.resultArr) {
        if(item==='error') {
          amount++
        }
      }
      return amount;
     },
     grade() {
      let k = this.amountRightAnswers/this.totalQuestions;
      let grade = false;
      if (k>=0 && k<0.2) {
        grade = 1
      };
      if (k>=0.2 && k<0.55) {
        grade = 2
      };
      if (k>=0.55 && k<0.7) {
        grade = 3
      };
      if (k>=0.7 && k<0.93) {
        grade = 4
      };
      if (k>=0.93 && k<1) {
        grade = 5
      };

      return grade;
     }
  }
}
</script>

<style lang="scss">
  $color-black: #000;
  $color-white: #fff;

  .app-verbs {
      position: relative;
      top: 0;
      left: 0;

      width: 100%;
      height: 100vh;

      display: block;
      overflow: hidden;

      font-size: 16px;

      color: #fff;
      background-image: url('assets/images/bg-1.jpg');
      background-repeat: no-repeat;
      background-position: center;
      background-size: cover;

      &::after {
          position: absolute;
          z-index: 0;
          top: 0;
          left: 0;

          width: 100%;
          height: 100%;

          display: block;

          content: '';

          background-color: rgba($color-black, 0.35);
      };

      .container {
          position: relative;
          z-index: 99;
      };

      &__level-block {
          width: 75%;
          margin: 40px auto;
      }

      .btn {
          &--level {
              margin: 5px;

              &.is-active {
                  border-color: #96ff00;
              }
          }
      }
  }

</style>
