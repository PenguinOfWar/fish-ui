<template>
  <div :class="classObject">
    <slot name="left"></slot>
    <textarea
      :placeholder="hint"
      :value="value" @input="updateValue($event.target.value)"
      v-if="type === 'textarea'"></textarea>
    <input :type="type"
           :placeholder="hint"
           :value="value" @input="updateValue($event.target.value)"
           autocomplete="off" v-else/>
    <i :class="iconClose" style="opacity: .6;" @click.stop="clearHandler" v-if="clear && !valueEmpty"></i>
    <i :class="icon" v-if="icon"></i>
    <i v-if="loading"></i>
    <slot name="right"></slot>
  </div>
</template>
<script>
  import { notify } from '../config'
  export default {
    name: 'fish-input',
    props: {
      value: { type: String, default: '' },
      type: { type: String, default: 'text' },
      hint: { type: String, default: 'Please ...' },
      icon: { type: String },
      iconLeft: { type: Boolean, default: false },
      iconClose: { type: String, default: 'fa fa-times-circle' },
      loading: { type: Boolean, default: false },
      size: { type: String },
      transparent: { type: Boolean, default: false },
      disabled: { type: Boolean, default: false },
      clear: { type: Boolean, default: false }
    },
    data () {
      return {
//        currentValue
      }
    },
    computed: {
      classObject () {
        return [
          'fish input',
          this.icon && this.iconLeft ? 'left' : 'right',
          {'loading': this.loading},
          this.size,
          {'transparent': this.transparent},
          {'disabled': this.disabled}
        ]
      },
      valueEmpty () {
        return /^\s*$/.test(this.value)
      }
    },
    methods: {
      updateValue (v) {
        this.changeHandler(v)
      },
      clearHandler () {
        this.changeHandler('')
      },
      changeHandler (v) {
        this.$emit('input', v)
        notify.field.change(this)
      }
    }
  }
</script>
