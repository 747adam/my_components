<template>
  <div class="scroll_demo">
    <h1>This is an demo page</h1>
    <h2>Scroller</h2>
    <Scroller
      :card-width="cardWidth"
      :rwd="rwd"
      :array-length="arrayData.length"
      @emitIsMove="emitIsMove"
    >
      <div
        v-for="(item, index) in cardData"
        :key="index"
        class="card"
        :style="{flex: `0 0 ${cardWidth}%`}"
      >
        <a
          draggable="false"
          :href="!isMove ? 'https://google.com.tw' : '#'"
          class="content"
        >
          <figure />
          <figcaption>{{ item.name }}</figcaption>
        </a>
      </div>
    </Scroller>
  </div>
</template>
<script>
import { ref, computed, onMounted, onBeforeUnmount } from '@vue/composition-api'
import Scroller from '@/components/Scroller.vue'
export default {
  name: 'ScrollDemo',
  components: {
    Scroller
  },
  setup () {
    let arrayData = ref([
      {
        name: 'pic_01'
      },
      {
        name: 'pic_02'
      },
      {
        name: 'pic_03'
      },
      {
        name: 'pic_04'
      },
      {
        name: 'pic_05'
      },
      {
        name: 'pic_06'
      }
    ])
    let ww = ref(0)
    let now = ref(1)
    let isMove = ref(false)
    const cardData = computed(() => {
      const result = []
        .concat(...arrayData.value)
        .concat(...arrayData.value)
        .concat(...arrayData.value)
      return result
    })
    const rwd = computed(() => {
      if (ww.value > 980) {
        return 5
      } else {
        return ww.value > 480 ? 3 : 1
      }
    })
    const cardWidth = computed(() => {
      if (arrayData.value.length) {
        const result = 100 / rwd.value
        return result
      }
    })
    const resizeWidth = () => {
      ww.value = document.body.offsetWidth
    }
    const emitIsMove = data => {
      isMove.value = data
    }
    onMounted(() => {
      window.addEventListener('resize', resizeWidth)
      resizeWidth()
    })
    onBeforeUnmount(() => {
      window.removeEventListener('resize', resizeWidth)
    })
    return {
      rwd,
      cardWidth,
      arrayData,
      now,
      cardData,
      emitIsMove,
      isMove
    }
  }
}
</script>

<style lang="scss">
.scroll_demo {
  margin: 0 auto;
  width: 100%;
  max-width: 900px;
  .card {
    padding: 0 5px;
    width: 100%;
    .content {
      display: flex;
      align-items: center;
      justify-content: center;
      padding-top: 75%;
      width: 100%;
      background: #cccccc;
    }
    figcaption {
      user-select: none;
    }
  }
}
</style>
