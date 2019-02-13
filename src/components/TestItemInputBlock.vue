<template>
	<div class="form-group row">
		<label for="'input_'" class="col-sm-4 col-form-label text-right">
			<b>{{ verbFormObject.name }}</b>
		</label>
		<div class="col-sm-7 form-control-wrap">
			<div class="form-control-wrap__msg" v-if="showInputMsg">
				{{correctVerb}}
			</div>
			<input
				type="text"
				class="form-control"
				v-bind:class="{'is-invalid': classError, 'is-valid': classSuccess}"
				v-bind:placeholder="verbFormObject.name"
				v-on:input="toHandleInput($event.target.value)"
				v-bind:value="inputVal"
				v-bind:disabled="inputDisabled"
				ref='input'>
		</div>
		<button
			class="col-sm-1 btn"
			type="button"
			v-on:click="toShowHint"
			v-bind:class="{'btn-danger': classError, 'btn-success': classSuccess, 'btn-warning': classDefault}">
			<b>&#927;</b>
		</button>
	</div>
</template>

<script>
export default {
  name: 'TestItemInputBlock',
  props: {
    resetInput: Boolean,
    val: String,
    verbFormObject: Object,
    currentAnswerObj: Object,
    comparingObj: Object,
    inputDisabled: Boolean,
    currentVerbObject: Object,
  },
  data: function() {
    return {
      inputVal: this.val,
      showInputMsg: false,
    }
  },
  methods: {
   toHandleInput(value) {
   	this.inputVal = value;
   	this.$emit('on-change-val', {inputVal: this.inputVal, verbForm: this.verbFormObject.var})
   },
   toResetInput() {
   	this.inputVal = '';
   	this.showInputMsg = false;
   },
   toShowHint() {
     if(this.comparingObj[this.verbFormObject.var] === 'error') {
     	this.showInputMsg = !this.showInputMsg;
     }
     
   }
  },
  computed: {
    classError() {
     if(this.comparingObj[this.verbFormObject.var] === 'error') {
     	return true;
     } else {
     	return false
     }
    },
    classSuccess() {
     if(this.comparingObj[this.verbFormObject.var] === 'success') {
     	return true;
     } else {
     	return false
     }
    },
    classDefault() {
     if(this.comparingObj[this.verbFormObject.var] != 'success' && this.comparingObj[this.verbFormObject.var] != 'error') {
     	return true;
     } else {
     	return false
     }
    },
    correctVerb() {
      return this.currentVerbObject[this.verbFormObject.var];
    }
  },
  watch: {
    resetInput: function () {
      this.toResetInput();
    },
  },
}
</script>

<style scoped lang="scss">
	.form-control-wrap {
		position: relative;

		&__msg {
			position: absolute;
			display: flex;
			align-items: center;
			padding-left: 15px;
			top: 0;
			font-size: 18px;
			width: 100%;
			background-color: #efe8a3;
			color: #dc3545;
			height: 100%;
			font-weight: 600;
			border-top-left-radius: 5px;
			border-bottom-left-radius: 5px;
		}
	}
</style>
