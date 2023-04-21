<template>
  <mainTvMaze msg="Helo"/>
</template>

<script>
import mainTvMaze from './components/mainTvMaze.vue'
import style from './assets/style.css'


export default {
  name: 'App',
  components: {
    mainTvMaze
  },
  assets : {
    style
  }
}
</script>
