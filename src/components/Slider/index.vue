<template>
  <div id="d-slider">
    <ul :style="wrapperStyle">
      <li v-for="img in newData" :key="img.id">
        <img :src="img.url" :alt="img.name" />
      </li>
    </ul>
    <div class="dots-wrap" v-if="hasDots">
      <div
        v-for="dot in newData"
        v-show="hideFirstAndLastDot(dot.id)"
        :key="dot.id"
        @click="handleDotAction(dot.id, true)"
        class="dot"
        :class="{ 'dot--active': currentImgLocation === dot.id }"
      ></div>
    </div>
    <div class="arrow-wrap" v-if="hasArrow">
      <div class="arrow arrow-left" @click="handleArrowAction(-1, true)"></div>
      <div class="arrow arrow-right" @click="handleArrowAction(1, true)"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Slider',
  components: {},
  props: {
    data: {
      type: Array,
      required: true,
    },
    duration: {
      type: Number,
      default: 2000,
    },
    transitionText: {
      type: String,
      default: `all 0.5s ease`,
    },
  },
  data() {
    return {
      wrapWidth: 100,
      wrpaTransform: 0,
      currentImgLocation: 1,
      transitionTextCache: this.transitionText,
      isLimit: false, // 限制箭頭點擊
      limitDuration: 500, // 時間內重複點擊箭頭會失效
    }
  },
  computed: {
    // 處理無縫切換效果
    newData() {
      const dataClone = Array.from(this.data)
      // 將第一張圖複製後插入陣列最後
      const firstImg = { ...this.data[0] }
      let firstImgId = this.data[this.data.length - 1].id
      firstImg.id = firstImgId + 1
      dataClone.splice(this.data.length, 0, firstImg)
      // 將最後一張圖複製後插入陣列最前
      const lastImg = { ...this.data[this.data.length - 1] }
      let lastImgId = 0
      lastImg.id = lastImgId
      dataClone.splice(0, 0, lastImg)
      return dataClone
    },
    wrapperStyle() {
      return {
        width: `${this.wrapWidth}%`,
        transform: `translateX(-${this.wrpaTransform}%)`,
        transition: this.transitionText,
      }
    },
    // 每張圖片的偏移量
    percentage() {
      return 100 / this.newData.length
    },
    hasDots() {
      return this.$attrs.hasOwnProperty('dots')
    },
    hasArrow() {
      return this.$attrs.hasOwnProperty('arrow')
    },
  },
  methods: {
    updatedTransitionText(val) {
      this.$emit('update:transitionText', val)
    },
    // 輪播到最後一張圖或第一張圖時進行特別處理
    // - 以動畫過渡方式偏移到額外插入的最後一張圖(第一張圖的clone)，之後關掉動畫過渡，瞬間切回第一張圖，第二步時因為沒有動畫過渡效果，視覺上看不出來，達到無縫輪播效果
    handleLastAndFisrtTurn(currentLocation, dir) {
      return new Promise(resolve => {
        this.currentImgLocation += dir
        this.wrpaTransform = this.currentImgLocation * this.percentage
        this.currentImgLocation = currentLocation
        resolve('done')
      })
    },
    handleThen() {
      this.updatedTransitionText(`none`)
      this.wrpaTransform = this.currentImgLocation * this.percentage
      this.resetTransitionText = setTimeout(() => {
        this.updatedTransitionText(this.transitionTextCache)
        clearTimeout(this.resetTransitionText)
        clearTimeout(this.timeoutTimer)
      }, 300)
    },
    // 隱藏額外插入的前後兩張圖的點點
    hideFirstAndLastDot(dotId) {
      return dotId !== 0 && dotId !== this.newData.length - 1
    },
    handleDotAction(id, isUserTriggered = false) {
      if (isUserTriggered) clearInterval(this.autoPlayTimer)
      this.updatedTransitionText(this.transitionTextCache)
      this.wrpaTransform = id * this.percentage
      this.currentImgLocation = id
      if (isUserTriggered) this.autoPlayTimer = setInterval(this.handleArrowAction, this.duration)
    },
    handleArrowAction(dir = 1, isUserTriggered = false) {
      if (this.isLimit) return
      if (isUserTriggered) {
        clearInterval(this.autoPlayTimer)
        this.isLimit = true
        const intervalLimitTimer = setTimeout(() => {
          this.isLimit = false
          clearTimeout(this.intervalLimitTimer)
        }, this.limitDuration)
      }
      const num = this.currentImgLocation
      if (num + dir === this.newData.length - 1) {
        this.handleLastAndFisrtTurn(1, dir).then(res => {
          this.timeoutTimer = setTimeout(this.handleThen, 450)
        })
      } else if (num + dir === 0) {
        this.handleLastAndFisrtTurn(this.data.length, dir).then(res => {
          this.timeoutTimer = setTimeout(this.handleThen, 450)
        })
      } else {
        this.updatedTransitionText(this.transitionTextCache)
        this.currentImgLocation += dir
        this.wrpaTransform = this.currentImgLocation * this.percentage
      }
      if (isUserTriggered) this.autoPlayTimer = setInterval(this.handleArrowAction, this.duration)
    },
    handleKeydown(e) {
      switch (e.keyCode) {
        case 37:
          this.handleArrowAction(-1, true)
          break
        case 39:
          this.handleArrowAction(1, true)
          break
        default:
          return
      }
    },
  },
  created() {
    this.wrapWidth = this.newData.length * 100
    console.log(this.newData)
  },
  mounted() {
    this.autoPlayTimer = setInterval(this.handleArrowAction, this.duration)
    addEventListener('keydown', this.handleKeydown)
  },
  beforeDestroy() {
    clearInterval(this.autoPlayTimer)
    removeEventListener('keydown', this.handleKeydown)
  },
}
</script>

<style scoped lang="scss">
#d-slider {
  margin-top: 50px;
  position: relative;
  width: 100%;
  min-width: 360px;
  height: auto;
  max-height: 250px;
  overflow: hidden;

  > ul {
    display: flex;
    transition: all 0.5s ease;
    li {
      width: 100%;
    }
  }

  img {
    max-width: 100%;
    height: auto;
  }
}
.dots-wrap {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  .dot {
    cursor: pointer;
    width: 10px;
    height: 10px;
    @include color-contrast('dylan', true);
    border-radius: 100%;
    margin-right: 5px;
    &--active {
      background-color: theme-color-level('dylan', 5);
    }
  }
}

.arrow {
  position: absolute;
  @include color-contrast('dylan', true);
  background-color: rgba(theme-color('dylan'), 0.5);
  transition: background-color 0.3 ease-in-out;
  top: 50%;
  width: 40px;
  height: 40px;
  cursor: pointer;
  border-radius: 100%;
  &:hover {
    opacity: 0.5;
  }
}
.arrow-left {
  left: 10px;
}
.arrow-right {
  right: 10px;
}
</style>
