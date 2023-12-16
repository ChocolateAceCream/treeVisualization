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
      left: null,
      right: null,
      val: null,
      color: null,
      parent: null,
    })
    const inputRef = ref()

    const onInsert = () => {
      let insertVal = Number(inputRef.value.value)
      let cur = tree.value

      if (cur.val === null) {
        cur.val = insertVal
        cur.color = 'black'
        return
      }
      let newNode = null
      while (cur.val != null) {
        if (insertVal < cur.val) {
          if (cur.left === null) {
            cur.left = {
              left: null,
              right: null,
              val: insertVal,
              color: 'red',
              parent: cur,
            }
            newNode = cur.left
            break
          } else {
            cur = cur.left
          }
        } else {
          if (cur.right === null) {
            cur.right = {
              left: null,
              right: null,
              val: insertVal,
              color: 'red',
              parent: cur,
            }
            newNode = cur.right
            break
          } else {
            cur = cur.right
          }
        }
      }
      repair(newNode)
      tree.value.color = 'black'
    }


    const repair = (cur) => {
      let parent = cur.parent
      if (parent === null) {
        cur.color = 'black'
        return
      }
      if (parent.color === 'black') {
        return
      }

      // parent is red, need to fix
      let grandParent = parent.parent
      if (grandParent === null) {
        parent.color = 'black'
        return
      }

      let uncle = grandParent.left === parent ? grandParent.right : grandParent.left
      if (uncle?.color === 'red') {
        uncle.color = 'black'
        parent.color = 'black'
        grandParent.color = 'red'
        repair(grandParent)
        return
      }

      let gpp = grandParent.parent
      let dir = null
      if (gpp === null) {
        dir = null
      } else if (gpp.left === grandParent) {
        dir = 'L'
      } else {
        dir = 'R'
      }

      let newNode = null
      if (cur === parent.left) {
        if (parent === grandParent.left) {
          //L, L
          parent.right = grandParent
          grandParent.left = null
          grandParent.parent = parent
          newNode = parent

        } else {
          //R, L
          cur.left = grandParent
          cur.right = parent
          parent.left = null
          grandParent.right = null
          parent.parent = cur
          grandParent.parent = cur
          newNode = cur
        }
      } else {
        if (parent === grandParent.left) {
          //L, R
          cur.left = parent
          cur.right = grandParent
          parent.right = null
          grandParent.left = null
          parent.parent = cur
          grandParent.parent = cur
          newNode = cur
        } else {
          //R, R
          parent.left = grandParent
          grandParent.right = null
          grandParent.parent = parent
          newNode = parent
        }
      }
      newNode.color = 'black'
      newNode.left.color = 'red'
      newNode.right.color = 'red'

      if (dir === null) {
        tree.value = newNode
      } else if (dir === 'L') {
        newNode.parent = gpp
        gpp.left = newNode
      } else {
        newNode.parent = gpp
        gpp.right = newNode
      }

    }

    const render = () => {
      let root = h('div', { style: "width:100%" }, [
        h('h1', 'Black Red Tree'),
        h('input', { ref: inputRef, style: "margin-left: 10px;" }, ['insert']),
        h('button', { style: "margin-left: 10px;", onClick: onInsert }, ['insert']),
      ])

      if (tree.value.val != null) {
        let child = iterator(tree.value)
        let t = h('ul', child)
        root.children.push(h('div', { class: 'tree' }, [t]))
      }

      return root
    }

    const iterator = (node) => {
      console.log('-----node---', node)
      let child = []
      if (node.left) {
        child.push(iterator(node.left))
      }
      if (node.right) {
        child.push(iterator(node.right))
      }
      if (child.length > 0) {
        return h('li', [h('span', {style: {background: node.color === 'black' ? '#a6979e' : '#e51c1c94'}}, node.val), h('ul', child)])
      } else {
        return h('li', h('span', { style: { background: node.color === 'black' ? '#a6979e' : '#e51c1c94' } }, node.val))
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

.tree li::before,
.tree li::after {
  content: '';
  position: absolute;
  top: 0;
  right: 50%;
  border-top: 1px solid #ccc;
  width: 51%;
  height: 10px;
}

.tree li::after {
  right: auto;
  left: 50%;
  border-left: 1px solid #ccc;
}

.tree li:only-child::after,
.tree li:only-child::before {
  display: none;
}

.tree li:only-child {
  padding-top: 0;
}

.tree li:first-child::before,
.tree li:last-child::after {
  border: 0 none;
}

.tree li:last-child::before {
  border-right: 1px solid #ccc;
  border-radius: 0px 5px 0px 0px;
}

.tree li:first-child::before {
  border-radius: 5px 0px 0px 0px;
}

.tree ul ul::before {
  content: '';
  position: absolute;
  top: 0;
  left: 50%;
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

// }</style>
