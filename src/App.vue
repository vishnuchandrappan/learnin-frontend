<template>
  <div id="app">
    <b-navbar variant="primary" type="dark">
      <b-navbar-brand href="#">TinkerHub</b-navbar-brand>
    </b-navbar>
    <b-container class="top-padding">
      <MentorsList></MentorsList>
    </b-container>
  </div>
</template>

<script>
import MentorsList from './components/MentorsList.vue'

export default {
  name: 'app',
  components: {
    MentorsList
  }
}
</script>

<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.top-padding {
  padding-top: 30px;
}
</style>
