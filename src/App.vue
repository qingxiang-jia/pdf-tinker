<template>
  <div id="app">
    <Header></Header>
    <file-holder></file-holder>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import FileHolder from './components/FileHolder.vue'

export default {
  name: 'app',
  components: {
    Header,
    FileHolder
  }
}
</script>

<style>
</style>
