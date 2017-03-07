<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>Vue Synched Columns</h1>
    <div>This module synchronizes scrolls of multiple columns</div>
    <synched-columns>
      <column class="first">
        <div v-for="n in 10">{{ n }}</div>
      </column>
      <column class="second">
        <div v-for="n in 10">{{ n }}</div>
      </column>
      <column class="third">
        <div v-for="n in 10">{{ n }}</div>
      </column>
    </synched-columns>
    <br>
  </div>
</template>

<script>
import { SynchedColumns, Column } from './components/SynchedColumns';

export default {
  name: 'app',
  components: {
    SynchedColumns,
    Column,
  },
};
</script>

<style scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
