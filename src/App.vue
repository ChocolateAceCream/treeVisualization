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
  </select>
  <BinaryTree v-if="treeType === 'binary'">
  </BinaryTree>
  <BTree v-if="treeType === 'b_tree'"></BTree>
</template>

<script>
import { defineComponent, toRefs, reactive } from 'vue'
import BinaryTree from './BinaryTree.vue'
import BTree from './BTree.vue'
export default defineComponent({
  components: {
    BinaryTree,
    BTree,
  },
  setup(props, ctx) {
    const state = reactive({
      treeType: 'b_tree',
    })
    return {
      ...toRefs(state)
    }
  }
})
</script>
<style lang='scss' scoped>
</style>