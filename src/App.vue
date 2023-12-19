<!--
* @fileName App.vue
* @author Di Sheng
* @date 2023/12/11 20:51:37
* @description
!-->
<template>
  <div>Tree Demo</div>
  <select v-model="treeType">
    <option value="binary">binary tree</option>
    <option value="b_tree">b tree</option>
    <option value="black_red_tree">black red tree</option>
      <option value="min_heap">min heap</option>
  </select>
  <BinaryTree v-if="treeType === 'binary'">
  </BinaryTree>
  <BTree v-if="treeType === 'b_tree'"></BTree>
  <MinHeap v-if="treeType === 'min_heap'"></MinHeap>
  <BlackRedTree v-if="treeType === 'black_red_tree'"></BlackRedTree>
</template>

<script>
import { defineComponent, toRefs, reactive } from 'vue'
import BinaryTree from './BinaryTree.vue'
import BTree from './BTree.vue'
import BlackRedTree from './BlackRedTree.vue'
import MinHeap from './MinHeap.vue'
export default defineComponent({
  components: {
    BinaryTree,
    BTree,
    BlackRedTree,
    MinHeap,
  },
  setup(props, ctx) {
    const state = reactive({
      treeType: 'min_heap',
    })
    return {
      ...toRefs(state)
    }
  }
})
</script>
<style lang='scss' scoped>
</style>