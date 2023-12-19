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
    const heap = ref([])
    const poppedVal = ref(null)
    const inputRef = ref()

    const onInsert = () => {
      let arr = heap.value
      let insertVal = Number(inputRef.value.value)
      arr.push(insertVal)
      let curIdx = arr.length - 1
      let parentIdx = Math.floor((curIdx - 1) / 2)
      while (parentIdx !== curIdx) {
        if (arr[parentIdx] > arr[curIdx]) {
          let temp = arr[curIdx]
          arr[curIdx] = arr[parentIdx]
          arr[parentIdx] = temp

          curIdx = parentIdx
          parentIdx = Math.floor((curIdx - 1) / 2)
          console.log(curIdx,parentIdx)
        } else {
          break
        }
      }
      heap.value = arr
    }

    const onPop = () => {
      let arr = heap.value
      let l = arr.length
      let temp = arr[0]
      arr[0] = arr[l - 1]
      arr[l - 1] = temp
      poppedVal.value = arr.pop()
      siftDown(arr)
    }

    const siftDown = (arr) => {
      const l = arr.length
      if (l <= 1) {
        return
      }
      let endIdx = l - 1
      let curIdx = 0
      while(2 * curIdx + 1 <= endIdx) {
        if (2 * curIdx + 2 > endIdx ){
          //no right child
          if (arr[2 * curIdx + 1] < arr[curIdx]) {
            const temp = arr[2 * curIdx + 1]
            arr[2 * curIdx + 1] = arr[curIdx]
            arr[curIdx] = temp
          }
          return
        }
        let smallerChildIdx = 2 * curIdx + 2
        if (arr[2 * curIdx + 1] < arr[2 * curIdx + 2]) {
          smallerChildIdx = 2*curIdx + 1
        }
        if (arr[smallerChildIdx] < arr[curIdx]) {
          const temp = arr[smallerChildIdx]
          arr[smallerChildIdx] = arr[curIdx]
          arr[curIdx] = temp
          curIdx = smallerChildIdx
        } else {
          return
        }
      }
    }

    const render = () => {
      let root = h('div', { style: "width:100%" }, [
        h('h1', 'Min Heap'),
        h('div', { style: "margin-top: 5px;" }, [
          h('input', { ref: inputRef, style: "margin-left: 10px;", type: 'number', value: '' }),
          h('button', { style: "margin-left: 10px;", onClick: onInsert }, ['insert']),
          h('button', { style: "margin-left: 20px;", onClick: onPop }, ['Pop']),
          h('span', { style: "margin-left: 10px;" }, ['Poped Value: ' + poppedVal.value]),
        ]),
      ])
      console.log('-----root---', root)
      let arr = heap.value
      if (arr.length !== 0) {
        let tree = iterator(arr, arr.length-1, 0)
        console.log('----tree---',tree.value)
        root.children.push(h('div', { class: 'tree' }, [h('ul', tree)]))
      }
      return root
    }



    const iterator = (arr, lastIdx, idx) => {
      let child = []
      if (idx *2 + 1 <= lastIdx) {
        child.push(iterator(arr, lastIdx, idx * 2 + 1))
        if (idx * 2 + 2 <= lastIdx) {
          child.push(iterator(arr, lastIdx, idx * 2 + 2))
        }
      }
      console.log('----child----', child)

      if (child.length > 0) {
        return h('li',  [h('span', arr[idx]), h('ul', child)])
      } else {
        return h('li', h('span', arr[idx]))
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
