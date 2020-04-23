<template>
  <div
    class="el_select"
    :class="color + (disabled ? ' disabled' : '')"
  >
    <div
      v-if="multiple"
      class="multiple_bar"
    >
      {{ selected && selected.length > 0 ? `已選擇${selected.length}項` : defaultText }}
    </div>
    <select
      ref="refSelect"
      :disabled="disabled"
      :size="size"
      :class="className"
      :multiple="multiple ? true : false"
      @focus="focusSelect()"
      @blur="blurSelect()"
      @change="changeSelect()"
    >
      <option
        v-if="!multiple && !selected"
        :title="defaultText"
      >
        {{ defaultText }}
      </option>
      <option
        v-for="(item, index) in list"
        :key="index"
        :title="item.text || ''"
        :value="item.value || ''"
        @mousedown="multiple ? clickMultiple(index) : ''"
      >
        {{ item.text || '' }}
      </option>
    </select>
  </div>
</template>

<script>
import { ref, computed } from '@vue/composition-api'
export default {
  name: 'Scroller',
  props: {
    disabled: {
      type: Boolean,
      default: false
    },
    list: {
      type: Array,
      default: () => []
    },
    defaultText: {
      type: String,
      default: '請選擇'
    },
    limit: {
      type: Number,
      default: 7
    },
    multiple: {
      type: Boolean,
      default: false
    },
    color: {
      type: String,
      default: 'orange'
    }
  },
  setup (props, context) {
    let size = ref(0)
    let isFocus = ref(false)
    let selected = ref(null)
    // 判斷是不是使用手機
    const isMobile = computed(() => {
      try { document.createEvent('TouchEvent'); return true } catch (e) { return false }
    })
    // 是否為復選 class name
    const className = computed(() => {
      if (props.multiple) {
        return isFocus.value ? 'multiple' : 'multiple blur'
      } else {
        return ''
      }
    })
    // 聚焦選單時
    const focusSelect = () => {
      isFocus.value = true
      if (isMobile.value) return
      let count = props.list.length
      if (count > props.limit) {
        size.value = props.limit
      } else {
        size.value = count
      }
    }
    // 失焦選單時
    const blurSelect = () => {
      isFocus.value = false
      size.value = 0
      context.refs.refSelect.scrollTop = 0
    }
    // 單選 選擇選項
    const changeSelect = () => {
      selected.value = selectedArr()
      context.emit('emitSelected', selected.value)
      if (!props.multiple) context.refs.refSelect.blur()
    }
    // 復選 選擇選項
    const clickMultiple = index => {
      if (!isMobile.value) {
        event.preventDefault()
        context.refs.refSelect.options[index].selected = !context.refs.refSelect.options[index].selected
        selected.value = selectedArr()
        context.emit('emitSelected', selected.value)
      }
    }
    // 取得選擇的選項
    const selectedArr = () => {
      let data = []
      for (let i = 0; i < context.refs.refSelect.selectedOptions.length; i++) {
        const target = context.refs.refSelect.selectedOptions[i]
        data.push({
          text: target.text,
          value: target.value
        })
      }
      return data
    }
    return {
      size,
      isFocus,
      isMobile,
      selected,
      className,
      focusSelect,
      blurSelect,
      changeSelect,
      clickMultiple
    }
  }
}
</script>

<style lang="scss">
.el_select {
  position: relative;
  width: 100%;
  height: 40px;
  font-size: 16px;
  line-height: 38px;
  select {
    overflow-y: auto;
    padding: 0 28px 0 8px;
    width: 100%;
    font-size: 16px;
    color: #292929;
    background: url('https://imgur.com/faWt2pe.png') no-repeat #f3f3f3 right 5px center;
    background-size: 16px 16px;
    background-color: #f3f3f3;
    border: 1px solid #ff7800;
    border-radius: 4px;
    outline: none;
    -moz-appearance: none;
    -webkit-appearance: none;
    appearance: none;
    user-select: none;
    cursor: pointer;
    line-height: 38px;
    box-sizing: inherit;
    /*IE隱藏箭頭樣式*/
    &::-ms-expand {
      display: none;
    }
    &:focus {
      position: relative;
      outline: none;
      /* for edge */
      z-index: 100;
    }
  }
  option {
    position: relative;
    margin-left: -8px;
    padding: 5px 8px;
    width: calc(100% + 52px);
    height: 32px;
    background-color: #ffffff;
    line-height: 32px;
    &:hover {
      color: #ff7800;
      box-shadow: 0 0 10px 100px #ffeedf inset;
    }
    &:checked , &:active , &:checked:after , &:active:after {
      color: #ff7800;
    }
    &:checked , &:active {
      font-weight: bold;
      color: transparent;
      box-shadow: 0 0 10px 100px #ffffff inset;
    }
    &:checked:after , &:active:after {
      position: absolute;
      top: 0;
      left: 0;
      display: block;
      padding: 5px 8px;
      content: attr(title);
    }
  }
  .multiple {
    padding-top: 38px;
    height: 0;
    &:focus , &:focus-within {
      padding-top: 0;
      height: auto;
    }
    option {
      display: flex;
      align-items: center;
      &:before {
        display: inline-block;
        margin-right: 5px;
        width: 16px;
        height: 16px;
        border: 1px solid #cccccc;
        border-radius: 3px;
        content: '';
      }
      &:checked:after , &:active:after {
        left: 21px;
      }
      &:checked:before , &:active:before {
        background: url('https://i.imgur.com/D3rvRs0.png') #ff7800 no-repeat center center;
        background-size: 70% auto;
        border-color: #ff7800;
      }
    }
    &.blur {
      overflow: hidden;
    }
  }
  .multiple_bar {
    position: absolute;
    top: 0;
    left: 0;
    padding: 0 28px 0 8px;
    width: 100%;
    height: 100%;
    text-align: left;
    background-position: right 5px center;
    background-repeat: no-repeat;
    background-size: 16px 16px;
    background-color: #f3f3f3;
    border: 1px solid #ff7800;
    border-radius: 4px;
    z-index: 10;
    cursor: pointer;
    pointer-events: none;
    background-image: url('https://imgur.com/faWt2pe.png');
    &:focus {
      display: none;
    }
  }
  &.blue {
    select , .multiple_bar {
      color: #292929;
      background-color: #f3f3f3;
      border-color: #00afb8;
    }
    option {
      &:hover {
        color: #00afb8;
        box-shadow: 0 0 10px 100px #e6f9fa inset;
      }
      &:checked , &:active , &:checked:after , &:active:after {
        color: #00afb8;
      }
    }
    .multiple {
      option {
        &:checked:before , &:active:before {
          background-color: #00afb8;
          border-color: #00afb8;
        }
      }
    }
  }
  &.disabled {
    select , .multiple_bar {
      color: #7e7e7e;
      background-color: #f3f3f3;
      border-color: #eeeeee;
    }
  }
}

/* for firefox */
@supports (-moz-appearance:none) {
  // @-moz-document url-prefix() {
  .el_select {
    select {
      padding: 0 28px 0 4px;
      option {
        margin-left: -4px;
        padding: 5px 28px 5px 8px;
        &:checked:after , &:active:after , &:before {
          display: none;
        }
        &:checked , &:active , &:checked:hover {
          box-shadow: none;
        }
      }
    }
  }
}

/* for ie11 */
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {
  .el_select {
    &:focus , &:focus-within {
      z-index: 100;
    }
    select {
      padding: 8px 0;
      option {
        width: 100%;
        &:checked , &:active {
          background-color: #ffffff;
        }
        &:hover {
          background-color: #e6f9fa;
        }
      }
    }
    .multiple {
      padding: 0;
    }
  }
}

/* for safari */
@media not all and (min-resolution:.001dpcm) {
  @supports (-webkit-appearance:none) {
    .el_select {
      select {
        padding: 0;
        text-indent: 10px;
        option {
          text-indent: 10px;
        }
      }
      .multiple {
        &:focus-within , &:focus {
          margin: 0;
        }
      }
    }
  }
}
</style>
