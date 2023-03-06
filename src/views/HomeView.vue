<template>
  <div class="home">
    <CesiumDemo />
  </div>
</template>

<script>
// @ is an alias to /src
import CesiumDemo from '@/components/CesiumDemo.vue'

export default {
  name: 'HomeView',
  components: {
    CesiumDemo
  }
}
</script>
<style lang="scss" scoped>
.home {
  width: 100%;
  height: 100%;
}
</style>
