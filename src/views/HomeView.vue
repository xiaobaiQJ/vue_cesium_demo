<template>
  <div class="home">
   <!--  <CesiumDemo /> -->
    <component :is='"CesiumMap"'></component>
  </div>
</template>

<script>
// @ is an alias to /src
import CesiumDemo from '@/components/CesiumDemo.vue'
import CesiumMap from '@/components/CesiumMap'
export default {
  name: 'HomeView',
  components: {
    CesiumDemo,
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
