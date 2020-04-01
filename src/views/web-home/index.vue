<template>
  <div class="home">
    <div class="d-container">
      <Slider :data="sliderData" :duration="2000" arrow dots />
    </div>
    <div>qwdw</div>
  </div>
</template>

<script>
export default {
  name: 'home',
  data() {
    return {
      sliderData: [
        {
          id: 0,
          name: 'img1',
          url:
            'https://images.unsplash.com/photo-1585628256598-be8081ee19ca?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=934&q=80',
        },
        {
          id: 1,
          name: 'img2',
          url:
            'https://images.unsplash.com/photo-1558980664-ce6960be307d?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1650&q=80',
        },
        {
          id: 2,
          name: 'img3',
          url:
            'https://images.unsplash.com/photo-1585737757692-28c2c33e5d95?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2250&q=80',
        },
        {
          id: 3,
          name: 'img4',
          url:
            'https://images.unsplash.com/photo-1585735119407-b037131a352b?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2250&q=80',
        },
      ],
    }
  },
  components: {},
  created() {},
  computed: {},
}
</script>

<style lang="scss" scoped>
.d-container {
  width: 100%;
  // max-width: 548px;
  margin: 0 auto;
  background-color: rgb(207, 207, 207);
}
</style>
