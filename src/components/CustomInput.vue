<template>
    <div class="input-container">
        <input type="number" autocomplete="off" id="inp" placeholder="" :value="modelValue" @input="updateInput">
        <span class="label">{{ title }}</span>
        <span class="focus-bg"></span>
    </div>
</template>

<script>
export default {
    data() {
        return {

        }
    },
    props: {
        title: "",
        modelValue: Number
    },
    emits: [
        'update:modelValue'
    ],
    methods: {
        updateInput(event) {
            this.$emit('update:modelValue', parseInt(event.target.value));
        }
    }
}
</script>

<style lang="sass" scoped>
@import '../assets/styles/style.sass'

$primary: #0077FF
$dark: #000

.input-container
  position: relative
  margin: auto
  max-width: 100px
  border-radius: 3px
  overflow: hidden

  .label
    position: absolute
    top: 20px
    left: 12px
    font-size: 16px
    color: rgba($dark,.5)
    font-weight: 500
    transform-origin: 0 0
    transform: translate3d(0,0,0)
    transition: all .2s ease
    pointer-events: none

  .focus-bg
    position: absolute
    top: 0
    left: 0
    width: 100%
    height: 100%
    background: rgba($dark,.05)
    z-index: -1
    transform: scaleX(0)
    transform-origin: left

  input
    -webkit-appearance: none
    appearance: none
    width: 100%
    border: 0
    font-family: inherit
    padding: 16px 12px 0 12px
    height: 56px
    font-size: 16px
    font-weight: 400
    background: rgba($dark,.02)
    box-shadow: inset 0 -1px 0 rgba($dark,.3)
    color: $dark
    transition: all .15s ease

    &:hover
      background: rgba($dark,.04)
      box-shadow: inset 0 -1px 0 rgba($dark,.5)
    
    &:not(:placeholder-shown)
      + .label
        color: rgba($dark,.8)
        transform: translate3d(0,-12px,0) scale(.75)
    
    &:focus
      background: rgba($dark,.05)
      outline: none
      box-shadow: inset 0 -2px 0 $primary
      + .label
        color: $primary
        transform: translate3d(0,-12px,0) scale(.75)
        + .focus-bg
          transform: scaleX(1)
          transition: all .1s ease
</style>