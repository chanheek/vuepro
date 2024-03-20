<template>
  <div>
    <div class="map"></div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'KakaoMap',
  data() {
    return {
      map: null
    };
  },
  setup() { },
  created() { },
  mounted() {
    if(window.kakao && window) {
      this.loadMap();
    } else {
      this.loadScript();
    }
  },
  unmounted() {},
  methods: {
    loadScript() {
      const script = document.createElement('script');
      script.src = 
      script.onload = () => window.kakao.maps.load(this.loadMap);
      document.head.appendChild(script)
    }
  },
  loadMap(){
    const container = document.getElementById('map');
    const option = {
      center: new window.kakao.maps.Lating(33.450701,126.570667),
      level: 3
    };
  },
  loadMaker() {
    const makerPosition = new window.kakao.maps.Lating(
      33.450701,
      126.570667
    );
    const maker  = new window.kakao.maps.Maker({
      position: makerPosition,
    });
    maker.setMap(this.map);
  },
  },
}); 
</script>

<style scoped>
.map {
  width: 100%;
  height: 400px;
}
</style>
