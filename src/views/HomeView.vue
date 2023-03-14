<template>
  <div class="home">
    <component :is='"CesiumMap"'></component>
  </div>
</template>

<script>
import CesiumMap from '@/pages/CesiumMap'
export default {
  name: 'HomeView',
  components: {
    CesiumMap
  }
}
</script>
<style lang="scss" scoped>
.home {
  width: 100%;
  height: 100%;
}
</style>
