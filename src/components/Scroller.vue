<template>
  <div
    ref="scrollerWidth"
    class="scroller"
    @mouseover="stopTimer"
    @mouseleave="setTimer"
  >
    <a
      class="scroller_btn left"
      @click="clickMove(-1)"
    />
    <div class="scroller_stage">
      <div
        class="scroller_track"
        :style="moveClass"
        @mousedown="touchstart"
        @mousemove="touching"
        @mouseup="touchend"
        @touchstart="touchstart"
        @touchmove="touching"
        @touchend="touchend"
        @transitionend="transitionend"
      >
        <slot />
      </div>
    </div>
    <a
      class="scroller_btn right"
      @click="clickMove(1)"
    />
  </div>
</template>

<script>
import { ref, computed, onMounted } from '@vue/composition-api'
export default {
  name: 'Scroller',
  props: {
    arrayLength: {
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
    let timer = ref(0)
    let touchStartX = ref(0)
    let touchstartTime = ref(0)
    let touchendTime = ref(0)
    // touchMoveing是否移動中
    let touchMoveing = ref(false)
    // distance手拖移%
    let distance = ref(0)
    // 4/1張卡片距離
    const overDistance = computed(() => props.cardWidth / 4)
    // firstPos初始位置%
    const firstPos = computed(() => (props.arrayLength - 1) * props.cardWidth)
    // movePos目前已移動的卡片距離%
    const movePos = computed(() => now.value * props.cardWidth)
    // handMove目前手動移到哪%
    const handMove = computed(() => firstPos.value + movePos.value + distance.value)
    let isMove = ref(false)
    const moveNumber = computed(() => {
      const handMoveMax = firstPos.value + (now.value + 1) * props.cardWidth
      const handMoveMin = firstPos.value + (now.value - 1) * props.cardWidth
      // 判斷是否為手動拖移
      if (!touchMoveing.value) {
        return (firstPos.value + movePos.value) * -1
      }
      // 判斷拖移距離不能超過一格
      if (handMove.value > handMoveMax) {
        return handMoveMax * -1
      } else if (handMove.value < handMoveMin) {
        return handMoveMin * -1
      } else {
        return handMove.value * -1
      }
    })
    const moveClass = computed(() => {
      return {
        transform: `translate3d(${moveNumber.value}%,0,0)`,
        transitionDuration: `${time.value}s`
      }
    })
    const transitionend = () => {
      time.value = 0
      if ((props.arrayLength > props.rwd && now.value > props.arrayLength) || now.value < (props.arrayLength * -1) + 2) {
        now.value = 1
      }
      status.value = false
    }
    const clickMove = num => {
      if (!status.value && props.arrayLength > props.rwd) {
        stopTimer()
        time.value = 0.3
        status.value = true
        now.value += num
        setTimer()
      }
    }
    const setTimer = () => {
      timer.value = setInterval(() => {
        if (!status.value && props.arrayLength > 1) {
          time.value = 0.3
          status.value = true
          now.value += 1
        }
      }, 3000)
      if (touchMoveing.value === true) touchend()
    }
    const stopTimer = () => {
      status.value = false
      clearInterval(timer.value)
      timer.value = ''
    }
    // 按下
    const touchstart = e => {
      stopTimer()
      touchMoveing.value = true
      touchstartTime.value = new Date().getTime()
      e.x ? touchStartX.value = e.x : touchStartX.value = e.targetTouches[0].pageX
    }
    // 按住移動
    const touching = e => {
      if (touchMoveing.value) {
        // movingDistance判斷移動距離
        const movingDistance = e.x ? touchStartX.value - e.x : touchStartX.value - e.targetTouches[0].pageX
        distance.value = movingDistance / context.refs.scrollerWidth.clientWidth * 100
      }
    }
    // 放開
    const touchend = () => {
      touchMoveing.value = false
      touchendTime.value = new Date().getTime()
      // 判斷是移動還是點連結
      if (touchendTime.value - touchstartTime.value > 100) {
        isMove.value = true
      } else {
        isMove.value = false
        setTimer()
      }
      context.emit('emitIsMove', isMove.value)
      // 判斷向前還向後
      if (handMove.value > (firstPos.value + movePos.value)) {
        // 是否移動超過4/1張卡
        handMove.value > firstPos.value + movePos.value + overDistance.value ? clickMove(1) : now.value += 0
      } else {
        handMove.value < firstPos.value + movePos.value - overDistance.value ? clickMove(-1) : now.value += 0
      }
      distance.value = 0
      status.value = false
    }
    onMounted(() => {
      setTimer()
    })
    return {
      now,
      time,
      status,
      moveClass,
      transitionend,
      overDistance,
      clickMove,
      setTimer,
      stopTimer,
      timer,
      isMove,
      touchStartX,
      touchMoveing,
      touchstart,
      touchend,
      touching
    }
  }
}
</script>

<style lang="scss">
  .scroller {
    position: relative;
    overflow: hidden;
    padding: 0 50px;
    width: 100%;
    a {
      font-weight: bold;
      color: #42b983;
    }
  }

  .scroller_btn {
    position: absolute;
    top: 50%;
    width: 40px;
    height: 40px;
    border: 1px solid #cccccc;
    border-radius: 50%;
    transform: translateY(-50%);
    &.left {
      left: 0;
    }
    &.right {
      right: 0;
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
