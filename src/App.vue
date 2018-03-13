<template>
  <div id="app">
    <SliderSecter ref="secter" :canvasWidth='width'
    :colorSecter='colorSecter' :origin='origin' :canvasHeight='height'/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld';
import SliderSecter from './components/SliderSecter';

export default {
  name: 'App',
  data() {
    return {
      width: 300,
      height: 300,
      origin: { x: 100, y: 100, r: 100, startdeg: 240, opendeg: 25, count: 5 },
      colorSecter: { bg: 'rgba(0,100,200,0.3)',
        selectedBg: 'green',
        borderColor: 'yellow',
        selectedBorderColor: 'black',
        textColor: 'pink' },
      textList: [],
    };
  },
  mounted() {
    this.$refs.secter.$on('change', (val) => {
      window.console.log(val);
    });
  },
  components: {
    HelloWorld, SliderSecter,
  },
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
