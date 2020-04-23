<template>
  <div id="scroll_nav">
    <nav>
      <a
        ref="navRef1"
        @click="scrollToEl('tit_1')"
      >選單一</a>
      <a
        ref="navRef2"
        @click="scrollToEl('tit_2')"
      >選單二</a>
      <a
        ref="navRef3"
        @click="scrollToEl('tit_3')"
      >選單三</a>
      <a
        ref="navRef4"
        @click="scrollToEl('tit_4')"
      >選單四</a>
    </nav>
    <section
      id="tit_1"
      ref="secRef1"
    >
      <h2>主題一</h2>
      <p>安裝：npm i velocity-animate</p>
      <p>可使用錨點動畫</p>
      <p>頁面：import Velocity from 'velocity-animate'</p>
    </section>
    <section
      id="tit_2"
      ref="secRef2"
    >
      <h2>主題二</h2>
    </section>
    <section
      id="tit_3"
      ref="secRef3"
    >
      <h2>主題三</h2>
    </section>
    <section
      id="tit_4"
      ref="secRef4"
    >
      <h2>主題四</h2>
    </section>
  </div>
</template>
<script>
import { ref, onMounted, onBeforeUnmount } from '@vue/composition-api'
import Velocity from 'velocity-animate'
export default {
  name: 'ScrollNav',
  setup () {
    let activeBlock = ref(null)
    const secRef1 = ref(null)
    const secRef2 = ref(null)
    const secRef3 = ref(null)
    const secRef4 = ref(null)
    const navRef1 = ref(null)
    const navRef2 = ref(null)
    const navRef3 = ref(null)
    const navRef4 = ref(null)
    const scrollToEl = id => {
      Velocity(document.getElementById(id), 'scroll', {
        offset: '0px',
        duration: 500,
        easing: 'ease-in'
      })
    }
    const handleScroll = () => {
      console.log()
      const secPos1 = secRef1.value
      const secPos2 = secRef2.value
      const secPos3 = secRef3.value
      const secPos4 = secRef4.value
      if (window.pageYOffset < secPos4.offsetTop + secPos4.offsetHeight - window.innerHeight) {
        navRef4.value.className = ''
        window.pageYOffset >= secPos1.offsetTop - 130 &&
        window.pageYOffset < secPos1.offsetTop + secPos1.offsetHeight - 160
          ? navRef1.value.className = 'active' : navRef1.value.className = ''
        window.pageYOffset >= secPos2.offsetTop - 160 &&
        window.pageYOffset < secPos2.offsetTop + secPos2.offsetHeight - 160
          ? navRef2.value.className = 'active' : navRef2.value.className = ''
        window.pageYOffset >= secPos3.offsetTop - 160 &&
        window.pageYOffset < secPos3.offsetTop + secPos3.offsetHeight - 160
          ? navRef3.value.className = 'active' : navRef3.value.className = ''
      } else {
        navRef4.value.className = 'active'
        navRef3.value.className = ''
      }
    }
    onMounted(() => {
      document.addEventListener('scroll', handleScroll)
    })
    onBeforeUnmount(() => {
      document.removeEventListener('scroll', handleScroll)
    })
    return {
      secRef1,
      secRef2,
      secRef3,
      secRef4,
      navRef1,
      navRef2,
      navRef3,
      navRef4,
      activeBlock,
      handleScroll,
      scrollToEl
    }
  }
}
</script>

<style lang="scss">
#scroll_nav {
  padding-top: 48px;
  nav {
    position: fixed;
    top: 80px;
    left: 0;
    width: 100%;
    font-size: 18px;
    background: #ffffff;
    border-bottom: 1px solid #cccccc;
    line-height: 28px;
    a {
      display: inline-block;
      padding: 10px;
      width: 100px;
      &.active {
        color: #ffffff;
        background: #42b983;
      }
    }
  }
  section {
    display: flex;
    justify-content: center;
    min-height: 600px;
    text-align: center;
    flex-direction: column;
    &:nth-of-type(1) {
      background-color: #dddddd;
    }
    &:nth-of-type(2) {
      background-color: #aaaaaa;
    }
    &:nth-of-type(3) {
      background-color: #999999;
    }
    &:nth-of-type(4) {
      background-color: #777777;
    }
  }
}
</style>
