<template>
  <div :class="['fish select tree-select', {'multiple': multiple}]"
       @click="clickHandler"
       @mouseover="mouseOverHandler"
       @mouseout="mouseOutHandler"
       v-clickoutside="awayHandler">
    <template v-if="multiple">
      <a class="tag" @click.stop=""
         v-for="(item, index) in checkedItems" :key="index">
        {{ item.title }}
        <i :class="iconClose" @click.stop="closeItemHandler(item)"></i>
      </a>
      <div class="text hint" v-html="valueEmpty ? hint : ''"></div>
    </template>
    <div :class="['text', {'hint': valueEmpty}]" v-else>
      {{ selectedItem && selectedItem.title || hint }}
    </div>
    <i class="fa fa-times-circle" style="opacity: .6;" @click.stop="clearHandler" v-if="showClear && !valueEmpty"></i>
    <i class="fa fa-angle-down" v-else></i>
    <div class="content" v-show="visible" @click.stop="">
      <fish-tree
          :data="data"
          :default-selected-key="selectedKey"
          :default-checked-keys="checkedKeys"
          :multiple="multiple"
          :expand="expand"
          :delimiter="delimiter"
          :iconCaretRight="iconCaretRight"
          :iconCaretDown="iconCaretDown"
          @item-click="itemClickHandler"
          @item-checked="itemCheckedHandler"></fish-tree>
    </div>
  </div>
</template>
<script>
  import clickoutside from '../directives/clickoutside'
  import { notify } from '../config'
  import fishTree from './Tree.vue'

  export default {
    components: {fishTree},
    name: 'fish-tree-select',
    directives: { clickoutside },
    props: {
      value: { type: [String, Number, Array], default: () => [] },
      data: { type: Array, required: true },
      hint: { type: String, default: 'Please' },
      expand: { type: Boolean, default: false },
      multiple: { type: Boolean, default: false },
      delimiter: { type: String, default: '-' },
      iconClose: { type: String, default: 'fa fa-close' },
      iconCaretRight: { type: String, default: 'fa fa-caret-right' },
      iconCaretDown: { type: String, default: 'fa fa-caret-down' }
    },
    data () {
      return {
        selectedItem: null,
        selectedKey: Array.isArray(this.value) ? (this.value[0] || '') : this.value || '',
        checkedItems: [],
        checkedKeys: this.multiple ? this.value || [] : [],
        showClear: false,
        visible: false
      }
    },
    mounted () {
      this.resetValuesWithData(this.data)
    },
    computed: {
      valueEmpty () {
        return Array.isArray(this.value) ? this.value.length <= 0 : (this.value === undefined || this.value === null || this.value.toString() === '')
      }
    },
    methods: {
      resetValuesWithData (items) {
        items && items.forEach((item) => {
          if (this.multiple) {
            if (this.checkedKeys.includes(item.key)) {
              this.checkedItems.push(item)
            }
          } else if (this.selectedKey === item.key) {
            this.selectedItem = item
          }
          this.resetValuesWithData(item.children)
        })
      },
      mouseOverHandler () {
        this.showClear = true
      },
      mouseOutHandler () {
        this.showClear = false
      },
      clickHandler () {
        this.visible = !this.visible
      },
      itemClickHandler (item) {
        this.emitChange(item.key)
        this.awayHandler()
      },
      itemCheckedHandler (checkedKeys) {
        this.emitChange(checkedKeys)
      },
      closeItemHandler (item) {
        this.emitChange(this.checkedKeys.filter((key) => key !== item.key))
      },
      clearHandler () {
        this.visible = false
        this.emitChange([])
      },
      emitChange (v) {
        if (this.multiple) {
          this.checkedKeys = v
          this.checkedItems = []
        } else {
          this.selectedKey = v || ''
        }
        this.resetValuesWithData(this.data)
        this.$emit('input', v)
        this.$emit('change', v)
        notify.field.change(this)
      },
      awayHandler () {
        this.visible = false
        this.showClear = false
      }
    }
  }
</script>
