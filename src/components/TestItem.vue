<template>
	<div class="test-item">
		<h5 class="test-item__counter">{{counter+'/'+totalCount}}</h5>
		<h2>{{currentVerbObject.rus}}</h2>
		<form class="test-item__form">
			<TestItemInputBlock
				v-bind:resetInput="resetInput"
				v-bind:verbFormObject="verbForm"
				v-bind:id="verbForm.var"
				v-bind:ref="verbForm.var"
				v-for="verbForm in verbForms"
				v-on:on-change-val="toUpdateAnswerObj"
				v-bind:currentAnswerObj="currentAnswerObj"
				v-bind:currentVerbObject="currentVerbObject"
				v-bind:comparingObj="currentComparingObj"
				v-bind:inputDisabled="inputDisabled">
			</TestItemInputBlock>
		</form>
		<div class="test-item__buttons">
			<div class="btn btn-primary btn-lg" v-on:click="toAnswer" v-if="showBtnToAnswer">
				To answer
			</div>
			<div class="btn btn-info btn-lg" v-on:click="toNext" v-if="showBtnToNext">
				To next
			</div>
			<div class="btn btn-success btn-lg" v-if="showBtnToResult" v-on:click="toResult">
				Go to result
			</div>
		</div>
		<div class="test-item__alert-block" v-if="readinessToNext">
			<div class="alert" v-bind:class="alertClass" role="alert">
				{{alertText}}
			</div>
		</div>
	</div>
</template>

<script>
import vars from './../vars.js'
import TestItemInputBlock from './TestItemInputBlock.vue'

export default {
  name: 'TestItem',
  props: {
    workingArray: Array,
    activeLevelObject: Object,
  },
  data: function() {
    return {
      resetInput: false,
      verbForms: vars.verbForms,
      currentVerbIndex: 0,
      currentVerbObject: this.workingArray[0],
      answerComparingArray: [],
      currentAnswerObj: {
        infinitive: '',
        pastSimple: '',
        pastParticiple: ''
      },
      currentComparingObj: {
        infinitive: '',
        pastSimple: '',
        pastParticiple: '',
      },
      readinessToNext: false,
      maxVerbAmount: this.workingArray.length,
      result: false,
      inputDisabled: false,
    }
  },
  components: {
    TestItemInputBlock,
  },
  methods: {
    toNextVerbIndex() {
	    if(this.currentVerbIndex+1 < this.maxVerbAmount) {
	      this.currentVerbIndex ++;
	    } else {

	    	return;
	    }
    },

    toResult() {
	    this.$emit('on-result', this.answerComparingArray);
    },

    toChangeVerbObject() {
      this.currentVerbObject = this.workingArray[this.currentVerbIndex];
    },

    toCheckFill() {
      let i = 0;
      for (let key in this.currentAnswerObj) {
        if (this.currentAnswerObj[key] === '') {
          this.readinessToNext = 'empty';
          return true;
        }
        i++;
      }

      if (i === 0) {
        this.readinessToNext = 'empty';
        return true;
      }
      return false
    },

    toUpdateAnswerObj(a) {
      this.currentAnswerObj[a.verbForm] = a.inputVal;
    },

    toHandleComaprisons() {
      for (let k in this.currentComparingObj) {
        if (this.currentComparingObj[k] === 'error') {
          this.readinessToNext = 'error'
          return;
        }
        if (this.currentComparingObj[k] === 'success') {
          this.readinessToNext = 'success'
        }
      }

    },

    toCheckTestEnd() {
    	if(this.currentVerbIndex+1 === +this.maxVerbAmount) {
	       this.currentVerbIndex = false;
	        return true
    	} else {
    		return false;
    	}
    },

    toCompare() {
      let compare = false;

      if (this.toCheckFill() === true) {
        console.log(8888)
        return;
      };

      console.log(9999)

      if(this.currentVerbIndex + 1 === this.maxVerbAmount) {
        this.result = true;
      }

      for (let key in this.currentAnswerObj) {
        let compare = (this.currentAnswerObj[key] === this.currentVerbObject[key]);

        if (compare) {
          this.currentComparingObj[key] = 'success';

        }
        if (!compare) {
          this.currentComparingObj[key] = 'error';

        }
      };
      this.inputDisabled = true;
      console.log(this.currentComparingObj);
      console.log(this.answerComparingArray);
    },

    toAddToAnswerComparingArray() {
      if (this.readinessToNext === 'empty') {
        return;
      } else {
        this.answerComparingArray.push(this.currentComparingObj);
        this.answerComparingArray.slice();
      }
    },

    toReset() {
      this.currentAnswerObj = {
        infinitive: '',
        pastSimple: '',
        pastParticiple: ''
      };
      this.currentComparingObj = {
        infinitive: '',
        pastSimple: '',
        pastParticiple: '',
      };
      this.resetInput = !this.resetInput;
      this.readinessToNext = false;
      this.inputDisabled = false;
    },

    toAnswer() {
      this.toCompare();
      this.toHandleComaprisons();
      this.toAddToAnswerComparingArray();
    },

    toNext() {
      this.toReset();
      this.toNextVerbIndex();

      return;
    },
  },

  computed: {
    counter() {
      return +this.currentVerbIndex + 1;
    },
    totalCounterAmount() {

    },
    inputClass() {

    },
    alertClass() {
      if (this.readinessToNext === 'empty') {
        return 'alert-warning';
      };
      if (this.readinessToNext === 'error') {
        return 'alert-danger';
      };
      if (this.readinessToNext === 'success') {
        return 'alert-success';
      };
    },
    alertText() {
      if (this.readinessToNext === 'empty') {
        return 'Не все поля заполнены';
      };
      if (this.readinessToNext === 'error') {
        return 'Вы ошиблись. Для просмотра верного ответа щелкните по красным кнопкам - справа от поля ввода.';
      };
      if (this.readinessToNext === 'success') {
        return 'Ответ правильный.';
      };
    },
    showBtnToAnswer() {
      if (this.readinessToNext === false || this.readinessToNext === 'empty') {
        return true;
      };
      if (this.readinessToNext === 'error' || this.readinessToNext === 'success' || this.result) {
        return false;
      }
    },
    showBtnToNext() {
      if (this.readinessToNext === false || this.readinessToNext === 'empty' || this.result) {
        return false;
      };
      if (this.readinessToNext === 'error' || this.readinessToNext === 'success') {
        return true;
      }
    },
    showBtnToResult() {
      if(this.result) {
      	return true;
      } else {
      	return false;
      }
    },
    totalCount() {
      return this.activeLevelObject.amount;
    },
  },

  watch: {
    currentVerbIndex: function () {
      this.toChangeVerbObject();
    },
  },
}
</script>

<style scoped lang="scss">
@import "./../vars.scss";

	.test-item {
		position: relative;
		display: block;
		padding: 50px 80px 40px;
		background-color: rgba($color-white, 0.3);
		border-radius: 12px;

		&__form {
			margin-top: 35px;
		}

		&__buttons {
			margin-top: 35px;
		};

		&__alert-block {
			margin-top: 35px;
		};

		&__counter {
			display: block;
			position: absolute;
			left: 15px;
			top: 15px;
		}
	}

	.alert-hide {

	}
</style>
