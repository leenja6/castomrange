<template>
  <div id="indication" class="indication">{{ indic }}%</div>
  <p><input type="hidden" v-model="indic" /></p>
  <div @mousedown="ret" ref="sliderElem" id="slider" class="slider">
    <div
      :style="`left:${posRight * number * 0.997}px`"
      ref="thumbElem"
      id="thumb"
      :class="['thumb', animThumbElem ? 'active' : '']"
    ></div>
  </div>

  <div class="indication_button">
    <p>
      <a
        @click="procentAdd(item)"
        v-for="item in indicProcent"
        :key="item"
        href="#"
        >{{ item }}%</a
      >
    </p>
  </div>
</template>
<script>
import { ref, onMounted, watch } from 'vue'
export default {
  setup() {
    const indicProcent = [25, 50, 75, 100]
    const number = ref(0.5)
    const animThumbElem = ref(true)
    const sliderElem = ref(null)
    const indic = ref(50)
    const thumbElem = ref(null)
    const posLeft = ref()
    const posRight = ref()
    const pos = ref()

    onMounted(() => {
      positionRight()
    })

    watch(indic, values => values)

    function procentAdd(items) {
      number.value = items / 100
      indic.value = items
      animThumbElem.value = true
    }

    function positionRight() {
      posRight.value =
        sliderElem.value.offsetWidth - thumbElem.value.offsetWidth
      thumbElem.value.ondragstart = function() {
        return false
      }
    }
    function ret(e) {
      posLeft.value = e.clientX - thumbElem.value.offsetWidth / 2 - 46
      console.log(posLeft.value)
      checkPos(posLeft.value, posRight.value)
      indic.value = Math.ceil((pos.value * 100) / posRight.value)
      const thumbCoords = getCoords(thumbElem.value)
      const shiftX = e.pageX - thumbCoords.left
      const sliderCoords = getCoords(sliderElem.value)
      document.onmousemove = function(e) {
        animThumbElem.value = false
        posLeft.value = e.pageX - shiftX - sliderCoords.left
        checkPos(posLeft.value, posRight.value)
        indic.value = Math.ceil(((pos.value - 5) * 100) / posRight.value)
      }
      document.onmouseup = function() {
        animThumbElem.value = true
        document.onmousemove = document.onmouseup = null
      }
      return false
    }
    function checkPos(posLeft, posRight) {
      pos.value =
        posLeft < 0 ? 5 : posLeft + 5 > posRight ? posRight - 5 : posLeft + 5
      thumbElem.value.style.left = pos.value + 'px'
    }
    function getCoords(elem) {
      const box = elem.getBoundingClientRect()
      return {
        left: box.left + pageXOffset,
      }
    }
    return {
      indic,
      sliderElem,
      thumbElem,
      posLeft,
      posRight,
      pos,
      ret,
      getCoords,
      positionRight,
      checkPos,
      indicProcent,
      procentAdd,
      number,
      animThumbElem,
    }
  },
}
</script>
