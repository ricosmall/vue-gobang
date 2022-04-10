<script setup lang="ts">
const BLANK = -1
const WHITE = 0
const BLACK = 1

const state = ref(Array.from({ length: 10 }, (_, x) => Array.from({ length: 10 }, (_, y) => BLANK)))
const step = ref(1)
const finish = ref(false)

function handleClick(x: number, y: number) {
  if (finish.value)
    return alert('Game is over')
  const item = state.value[x][y]
  if (item !== BLANK)
    return
  step.value += 1
  state.value[x][y] = step.value % 2 === 0 ? WHITE : BLACK
  checkIfWin(x, y)
}

function checkIfWin(x: number, y: number) {
  const item = state.value[x][y]
  if (item === BLANK)
    return
  const directions = [
    [
      [0, -4],
      [0, -3],
      [0, -2],
      [0, -1],
      [0, 0],
      [0, 1],
      [0, 2],
      [0, 3],
      [0, 4],
    ],
    [
      [-4, 0],
      [-3, 0],
      [-2, 0],
      [-1, 0],
      [0, 0],
      [1, 0],
      [2, 0],
      [3, 0],
      [4, 0],
    ],
    [
      [-4, -4],
      [-3, -3],
      [-2, -2],
      [-1, -1],
      [0, 0],
      [1, 1],
      [2, 2],
      [3, 3],
      [4, 4],
    ],
    [
      [-4, 4],
      [-3, 3],
      [-2, 2],
      [-1, 1],
      [0, 0],
      [1, -1],
      [2, -2],
      [3, -3],
      [4, -4],
    ],
  ]
  const checkDirection = (x: number, y: number, direction: number[][]) => {
    const notBlankItems = direction.map(([dx, dy]) => {
      const nextX = x + dx
      const nextY = y + dy
      if (nextX < 0 || nextX > 9 || nextY < 0 || nextY > 9)
        return false
      const nextItem = state.value[nextX][nextY]
      if (nextItem === item)
        return true
      else
        return false
    })
    let win = false
    let count = 0
    notBlankItems.forEach((item) => {
      if (item) {
        count += 1
        if (count >= 5)
          win = true
      }
      else {
        count = 0
      }
    })
    return win
  }
  const finalWin = directions.some((direction) => {
    const win = checkDirection(x, y, direction)
    if (win) {
      finish.value = true
      return true
    }
    return false
  })
  if (finalWin)
    alert(`${item === WHITE ? '白' : '黑'}色获胜`)
}

function reset() {
  state.value = Array.from({ length: 10 }, (_, x) => Array.from({ length: 10 }, (_, y) => BLANK))
  step.value = 1
  finish.value = false
}
</script>

<template>
  <div>
    <h1 text-xl mb-5>
      Vue Gobang
    </h1>
    <div flex flex-col justify-center items-center>
      <div v-for="row, x in state" :key="x" flex>
        <button
          v-for="item, y in row"
          :key="y"
          w-10 h-10
          border="0.5px solid gray"
          :class="{'hover:bg-gray-500/20': item === BLANK && !finish }"
          flex justify-center items-center
          @click="handleClick(x, y)"
          @contextmenu.prevent="handleClick(x,y)"
        >
          <div v-if="item === BLANK" />
          <div v-else-if="item === WHITE" justify-center>
            <div i-carbon-checkmark />
          </div>
          <div v-else justify-center>
            <div i-carbon-close />
          </div>
        </button>
      </div>
    </div>

    <div>
      <button
        class="m-3 text-sm btn"
        @click="reset()"
      >
        Reset
      </button>
    </div>
  </div>
</template>
