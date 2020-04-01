<template>
  <div id="d-slider">
    <ul :style="getWrapWidth">
      <li v-for="img in newData" :key="img.id">
        <img :src="img.url" :alt="img.name" />
      </li>
    </ul>
    <div class="dots-wrap" v-if="hasDots">
      <div
        v-for="dot in data"
        :key="dot.id"
        @click="handleDotAction(dot.id)"
        class="dot"
        :class="{ 'dot--active': currentImgLocation === dot.id }"
      ></div>
    </div>
    <div class="arrow-wrap" v-if="hasArrow">
      <div class="arrow arrow-left" @click="handleArrow(-1)"></div>
      <div class="arrow arrow-right" @click="handleArrow(1)"></div>
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
  },
  data() {
    return {
      wrapWidth: 100,
      wrpaTransform: 0,
      currentImgLocation: 0,
    }
  },
  computed: {
    newData() {
      const firstImg = { ...this.data[0] }
      let id = this.data[this.data.length - 1].id
      firstImg.id = id + 1
      const dataClone = Array.from(this.data)
      dataClone.splice(this.data.length, 0, firstImg)
      return dataClone
    },
    getWrapWidth() {
      return {
        width: `${this.wrapWidth}%`,
        transform: `translateX(-${this.wrpaTransform}%)`,
      }
    },
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
    handleDotAction(id) {
      this.wrpaTransform = id * this.percentage
      this.currentImgLocation = id
    },
    handleArrow(dir) {
      const num = this.currentImgLocation
      if (num + dir === this.data.length) {
        this.currentImgLocation = 0
      } else if (num + dir < 0) {
        this.currentImgLocation = this.data.length - 1
      } else {
        this.currentImgLocation += dir
      }
      this.wrpaTransform = this.currentImgLocation * this.percentage
    },
    autoSlide() {
      this.currentImgLocation += 1
      if (this.currentImgLocation === this.newData.length - 1) this.currentImgLocation = 0
      if (this.currentImgLocation < this.newData.length) {
        this.wrpaTransform = this.currentImgLocation * this.percentage
      } else {
        this.currentImgLocation = 0
      }
    },
  },
  created() {
    this.wrapWidth = this.newData.length * 100
  },
  mounted() {
    setInterval(this.autoSlide, this.duration)
  },
  watch: {},
  beforeDestroy() {
    clearInterval(this.autoSlide)
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
  top: 50%;
  width: 20px;
  height: 20px;
  cursor: pointer;
  border-radius: 100%;
}
.arrow-left {
  left: 10px;
}
.arrow-right {
  right: 10px;
}
</style>
