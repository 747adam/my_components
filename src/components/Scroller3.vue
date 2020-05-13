<template>
  <div
    ref="scrollerWidth"
    class="stu_scroller"
  >
    <a
      v-if="now !==1 && ww < 960"
      class="btn_arr left"
      @click="clickMove(-1)"
    />
    <div class="scroller_stage">
      <div
        class="scroller_track"
        :style="moveClass"
        @transitionend="transitionend"
      >
        <slot />
      </div>
    </div>
    <a
      v-if="now < arrayLength - rwd + 1 && ww < 960"
      class="btn_arr right"
      @click="clickMove(1)"
    />
  </div>
</template>

<script>
import { ref, computed, onMounted, onBeforeUnmount } from '@vue/composition-api'
export default {
  name: 'Scroller',
  props: {
    arrayLength: {
      type: Number,
      default: 0
    },
    ww: {
      type: Number,
      default: 0
    },
    rwd: {
      type: Number,
      default: 0
    },
    cardWidth: {
      type: Number,
      default: 0
    }
  },
  setup (props, context) {
    let now = ref(1)
    let time = ref(0)
    let status = ref(false)
    let isMove = ref(false)
    const moveNumber = computed(() => {
      if (now.value === 1) {
        return (now.value - 1) * props.cardWidth * -1
      } else if (now.value === props.arrayLength - parseInt(props.rwd - 1)) {
        return (now.value - 1.5) * props.cardWidth * -1
      }
      return (now.value - 1.25) * props.cardWidth * -1
    })
    const moveClass = computed(() => {
      return {
        transform: `translate3d(${moveNumber.value}%,0,0)`,
        transitionDuration: `${time.value}s`
      }
    })
    const reNow = () => {
      now.value = 1
    }
    const transitionend = () => {
      time.value = 0
      status.value = false
    }
    const clickMove = num => {
      if (!status.value && props.arrayLength > props.rwd) {
        time.value = 0.3
        status.value = true
        now.value += num
      }
    }
    onMounted(() => {
      window.addEventListener('resize', reNow)
    })
    onBeforeUnmount(() => {
      window.removeEventListener('resize', reNow)
    })
    return {
      now,
      time,
      status,
      moveClass,
      reNow,
      transitionend,
      clickMove,
      isMove
    }
  }
}
</script>

<style lang="scss">
  .stu_scroller {
    position: relative;
    overflow: hidden;
    margin-bottom: 50px;
    width: 100%;
    a {
      font-weight: bold;
      color: #42b983;
    }
    .btn_arr {
      position: absolute;
      top: 50%;
      width: 32px;
      height: 32px;
      background: url('~@/assets/images/icon-arr.png') no-repeat center center #ffffff;
      background-size: 50% auto;
      border-radius: 50%;
      z-index: 5;
      box-shadow: 0 0 4px 0 rgba(41, 41, 41, .2);
      transform: translateY(-50%);
      cursor: pointer;
      &.left {
        left: 10px;
        transform: translateY(-50%) scaleX(-1);
      }
      &.right {
        right: 10px;
      }
    }
  }

  .scroller_stage {
    position: relative;
    overflow: hidden;
  }

  .scroller_track {
    position: relative;
    display: flex;
    align-items: top;
    width: 100%;
    height: 100%;
    transform-style: preserve-3d;
  }
</style>
