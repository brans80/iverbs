<template>
  <button type="button" 
          class="btn btn-lg btn--level btn-outline-light"
          v-bind:class="{ 'is-active': isActive }"
          v-on:click="changeLevel"
          >
          {{'level: '+curLevelObject.level +' '+ curLevelObject.name}}
  </button>
</template>

<script>


export default {
  name: 'LevelButton',
  props: {
    curLevelObject: Object,
    activeLevelObject: Object,
    levels: Array,
  },
  methods: {
    changeLevel() {
      this.$emit('on-change-level', this.curLevelObject)
    }
  },
  computed: {
     isActive() {
      return(+this.curLevelObject.level === +this.activeLevelObject.level)
     }
   }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
