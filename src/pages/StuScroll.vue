<template>
  <div class="scroll_demo">
    <h2>學生專區</h2>
    <ScrollerJob
      :card-width="cardWidth"
      :rwd="rwd"
      :ww="ww"
      :next-card="nextCard"
      :array-length="jobArray.length"
      @emitIsMove="emitIsMove"
    >
      <div
        v-for="(item, index) in jobCardData"
        :key="index"
        class="card"
        :style="{flex: `0 0 ${cardWidth}%`}"
      >
        <a
          draggable="false"
          :href="!isMove ? 'https://google.com.tw' : '#'"
          class="content"
          :target="!isMove ? 'name' : ''"
        >
          <figure />
          <figcaption>{{ item.name }}</figcaption>
        </a>
      </div>
    </ScrollerJob>
  </div>
</template>
<script>
import { ref, computed, onMounted, onBeforeUnmount } from '@vue/composition-api'
import ScrollerJob from '@/components/ScrollerJob.vue'
export default {
  name: 'StuScroll',
  components: {
    ScrollerJob
  },
  setup () {
    let jobArray = ref([
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
      }
    ])
    let ww = ref(0)
    let now = ref(1)
    let isMove = ref(false)
    // nextCard : scroll頭、尾是否露出前後張卡內容
    let nextCard = ref(true)
    const jobCardData = computed(() => {
      if (jobArray.value.length > rwd.value) {
        const result = []
          .concat(...jobArray.value)
          .concat(...jobArray.value)
          .concat(...jobArray.value)
        return result
      } else {
        return jobArray.value
      }
    })
    const rwd = computed(() => {
      if (ww.value > 980) {
        return 5
      } else {
        return ww.value > 480 ? 3 : 1
      }
    })
    const cardWidth = computed(() => {
      if (jobArray.value.length) {
        const result = jobArray.value.length > rwd.value && nextCard.value ? 100 / (rwd.value + 0.5) : 100 / rwd.value
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
      ww,
      nextCard,
      cardWidth,
      jobArray,
      now,
      jobCardData,
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
