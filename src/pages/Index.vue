<template>
  <div class="home">
    <h2>select 自定css樣式測試</h2>
    <div class="content">
      <ElSelect
        :limit="5"
        :list="selectList"
        @emitSelected="emitSelected1"
      />
      <ElSelect
        :limit="5"
        :list="selectList"
        :multiple="true"
        @emitSelected="emitSelected2"
      />
    </div>
  </div>
</template>

<script>
import { ref } from '@vue/composition-api'
import ElSelect from '@/components/ElSelect.vue'
export default {
  name: 'Home',
  components: {
    ElSelect
  },
  setup () {
    const selectList = ref([
      {
        value: 'a1',
        text: '選項1'
      },
      {
        value: 'a2',
        text: '選項2'
      },
      {
        value: 'a3',
        text: '選項3'
      },
      {
        value: 'a4',
        text: '選項4'
      },
      {
        value: 'a5',
        text: '選項5'
      },
      {
        value: 'a6',
        text: '選項6'
      },
      {
        value: 'a7',
        text: '選項7'
      }
    ])
    let selected1 = ref(null)
    let selected2 = ref(null)
    const emitSelected1 = data => {
      selected1.value = data
    }
    const emitSelected2 = data => {
      selected2.value = data
    }
    return {
      selectList,
      selected1,
      selected2,
      emitSelected1,
      emitSelected2
    }
  }
}
</script>

<style lang="scss">
.body {
  text-align: center;
}

h2 {
  margin-bottom: 30px;
  font-size: 30px;
  text-align: center;
  line-height: 40px;
}

.content {
  position: relative;
  margin: 0 auto;
  width: 130px;
}

.el_select {
  margin-bottom: 20px;
}
</style>
