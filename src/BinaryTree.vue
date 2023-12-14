<!--
* @fileName App.vue
* @author Di Sheng
* @date 2023/11/05 12:00:56
* @description
!-->
<script>
import { ref, h } from 'vue'

export default {
  props: {
    /* ... */
  },
  setup(props) {
    const tree = ref({
      val: 1,
      left: {
        val: 12,
        left: { val: 24, left: null, right: null },
        right: { val: 25, left: null, right: null }
      },
      right: {
        val: 53,
        left: {val:34, left: null, right: null},
        right: {val:45, left: null, right:null}
      }

    })
    const inputRef = ref()

    const onInsert = () => {
      let done = false
      let insertVal = Number(inputRef.value.value)
      let cur = tree.value
      let parent = null
      while (!done) {
        parent = cur
        if (insertVal > cur.val) {
          if (cur.right) {
            cur = cur.right
          } else {
            cur.right = {
              val: insertVal,
              depth: 0,
              left: null,
              right: null,
              parent: cur
            }
            done = true
          }
        } else {
          if (cur.left) {
            cur = cur.left
          } else {
            cur.left = {
              val: insertVal,
              depth: 0,
              left: null,
              right: null,
              parent: cur
            }
            done = true
          }
        }
      }
    }

    const treeFormatter = (node,parentNode) => {
      node.parent = parentNode
      let depth = 0
      console.log('-treeFormatter--',node)

      if (node.left) {
        node.left = treeFormatter(node.left, node)
        console.log('-node.left--', node.left)
        depth = node.left.depth + 1
      }
      if (node.right) {
        node.right = treeFormatter(node.right, node)
        depth = Math.max(depth, node.right.depth + 1)
      }
      node.depth = depth
      return node
    }


    const render = () => {
      let root = h('div', { style: "width:100%" }, [
        h('h1', 'Binary Tree'),
        h('button',  {onClick:()=>{console.log('--update----')}}, ['update']),
        h('input', {ref: inputRef,style: "margin-left: 10px;"}, ['insert']),
        h('button', { style: "margin-left: 10px;", onClick: onInsert }, ['insert']),
      ])
      console.log('-----root---', root)
      treeFormatter(tree.value, null)
      let child = iterator(tree.value)

      let t = h('ul',  child)
      root.children.push(h('div', { class: 'tree' }, [t]))
      return root
    }

    const iterator = (node, position=0) => {
      console.log('---node--', node)
      let c = ''
      if (position == 1) {
        c = 'left'
      } else if (position == 2) {
        c = 'right'
      } else {
        c = ''
      }
      let child = []
      if (node && node.left ){
        child.push(iterator(node.left, 1))
      }
      if (node && node.right){
        child.push(iterator(node.right,2))
      }
      if (child.length > 0) {
        return  h('li',  {class: c,},[h('span', node.val), h('ul', child)])
      } else {

        return h('li', {class: c},h('span',node.val ))
      }
    }

    // 返回渲染函数
    return () => render()
  }
}
</script>
<style lang='scss' scoped>
body {
  height: 100vh;
  display: grid;
  align-items: center;
}
.tree {
  width: 100%;
  height: auto;
  text-align: center;
}
.tree ul {
  padding-top: 20px;
  position: relative;
  transition: 0.5s;
}

.tree li {
  display: inline-table;
  text-align: center;
  list-style-type: none;
  position: relative;
  padding: 10px;
  transition: 0.5s;
}

.tree li::before, .tree li::after{
  content: '';
  position: absolute;
  top:0;
  right:50%;
  border-top: 1px solid #ccc;
  width: 51%;
  height: 10px;
}

.tree li::after{
  right: auto;
  left:50%;
  border-left: 1px solid #ccc;
}

.tree li:only-child::after, .tree li:only-child::before{
  display: none;
}

.tree li:only-child {
  padding-top: 0;
}

.tree li:first-child::before, .tree li:last-child::after{
  border: 0 none;
}

.tree li:last-child::before{
  border-right: 1px solid #ccc;
  border-radius: 0px 5px 0px 0px;
}

.tree li:first-child::before{
  border-radius: 5px 0px 0px 0px;
}

.tree ul ul::before{
  content: '';
  position: absolute;
  top:0;
  left:50%;
  border-left: 1px solid #ccc;
  width: 0;
  height: 20px;
}

.tree span {
  border: 1px solid #ccc;
  padding: 10px;
  display: inline-grid;
  border-radius: 5px;
  text-decoration-line: none;
  border-radius: 5px;
  transition: .5s;
}

// .tree span {
//   border: 1px solid #ccc;
//   border-radius: 5px;
//   color: #666;
//   padding:8px;
//   font-size: 12px;
//   text-transform: uppercase;
//   letter-spacing: 1px;
//   font-weight: 500;

// }
</style>
