<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>Vue Synched Columns</h1>
    <div>This module synchronizes scrolls of multiple columns</div>
    <synched-columns></synched-columns>
    <br>
  </div>
</template>

<script>
import SynchedColumns from './components/SynchedColumns';

export default {
  name: 'app',
  components: {
    SynchedColumns,
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
  margin-top: 60px;
}
</style>
