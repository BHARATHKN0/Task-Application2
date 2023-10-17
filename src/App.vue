<template>
  <div id="app">
    <task-app></task-app>
  </div>
</template>
<script>
  import taskApp from './components/task-app.vue'
  export default {
    name: 'App',
    components:{
      taskApp
    }
  }
</script>
