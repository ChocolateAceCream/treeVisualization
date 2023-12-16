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
      val: [4],
      isLeaf: true,
      children: null,
      parent: null,
    })
    const inputRef = ref()
    const degreeRef = ref()

    const onReset = () => {
      // tree = ref({
      //   val: [4],
      //   isLeaf: true,
      //   children: null,
      //   parent: null,
      // })
    }

    const onInsert = () => {
      console.log('--onInsert---',tree.value)
      let insertVal = Number(inputRef.value.value)
      let maxL = Number(degreeRef.value.value) * 2 - 1
      let cur = tree.value

      while (!cur.isLeaf) {
        const l = cur.val.length
        if (insertVal > cur.val[ l - 1]) {
          cur = cur.children[l]
          cur.parentIdx = l
          continue
        }
        for (let i = 0; i < l; i++) {
          if (insertVal < cur.val[i]) {
            cur = cur.children[i]
            cur.parentIdx = i
            break
          }
        }
      }

      // reached the leaf node, do insert here
      cur.val.push(insertVal)
      cur.val.sort((a, b) => a - b)
      let count = 0
      let head = cur
      while (head.val.length >= maxL) {
        count++
        // split cur, then pop to upper
        let splitIdx = degreeRef.value.value - 1
        let curLeft = {
          val: head.val.slice(0, splitIdx),
          isLeaf: head.isLeaf,
        }
        let curRight = {
          val: head.val.slice(splitIdx+1),
          isLeaf: head.isLeaf,
        }
        if (head.parent === null) {
          let newParentNode = {
            val: [head.val[splitIdx]],
            isLeaf: false,
            parent: null,
            children: [curLeft, curRight],
          }
          curLeft.parent = newParentNode
          curRight.parent = newParentNode
          tree.value = newParentNode
          if (count === 1) {
            // only pop one layer upper
            return
          }
        } else {
          //cur has a parent
          curLeft.parent = head.parent
          curRight.parent = head.parent
          head.parent.val.push(head.val[splitIdx])
          head.parent.val.sort((a, b) => a - b)
          head.parent.children.splice(head.parentIdx, 2, curLeft, curRight)
        }

        // split cur's children
        if (head.children?.length) {
          const middleIndex = Math.floor(head.children.length / 2)
          const firstHalf = head.children.slice(0, middleIndex)
          const secondHalf = head.children.slice(middleIndex)
          curLeft.children = firstHalf
          curRight.children = secondHalf

          firstHalf.forEach(child => {
            child.parent = curLeft
          })

          secondHalf.forEach(child => {
            child.parent = curRight
          })
        }

        head = head.parent
      }

      //

    }

    const render = () => {
      console.log('------render----')
      let root = h('div', { style: "width:100%", display: 'flex', flex: '1' }, [
        h('h1', 'B Tree'),
        h('div', {style: "margin-top: 5px;"}, [
          h('input', { ref: degreeRef, style: "margin-left: 10px;", type: 'number', value: '2' }, ['degree']),
          h('button', { style: "margin-left: 10px;", onClick: onReset }, ['Degree']),
          h('span', { style: "margin-left: 10px;" }, ['Max length is 2 * Degree -1']),
        ]),
        h('div', {style: "margin-top: 5px;"}, [
        h('input', { ref: inputRef, style: "margin-left: 10px;" }, ['insert']),
          h('button', { style: "margin-left: 10px;", onClick: onInsert }, ['insert']),
        ]),
      ])
      let child = iterator(tree.value)

      let t = h('ul', child)
      root.children.push(h('div', { class: 'tree' }, [t]))
      return root
    }

    const iterator = (node) => {
      console.log('-----node---', node)

      let s = node.val.join(',')
      console.log('---s--', s)
      if (node.children && node.children.length) {
        let subNodes = []
        node.children.forEach(child => {
          subNodes.push(iterator(child))
        })
        return h('li', [h('span', s), h('ul', subNodes)])
      }

      return h('li',h('span', s))
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
