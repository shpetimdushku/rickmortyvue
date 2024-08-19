<template>
  <div id="app">
    <UserAuth />
  </div>
</template>

<script>
import UserAuth from './components/UserAuth.vue';
import './assets/style.css';

export default {
  name: 'App',
  components: {
    UserAuth
  }
};
</script>