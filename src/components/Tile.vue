<template>
  <div :class="'tile tile-'+ color + (isActivated ? ' activated' : '') + (isClickable ? '' : ' unclickable')" @click='clickFn(color)'></div>
  
</template>

<script>

export default {
  name: 'Tile',
  props: [
    'color',
    'isActivated',
    'isClickable',
    'clickFn'
  ],
  created: function() {

  }
}
</script>

<style lang="sass">

</style>
