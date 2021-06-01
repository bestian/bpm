<template>
  <div id="app">
    <div class="no-print ui menu">
      <router-link to="/" class = "item">Home</router-link>
      <router-link to="/about" class = "item">About</router-link>
      <router-link to="/game" class = "item">Game</router-link>
      <router-link to="/word/萌" class = "item">Quiz</router-link>
    </div>
    <router-view/>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

.router-link-exact-active {
  background-color: #42b983 !important;
}

@media print {
  .no-print {
    display: none;
    visibility: hidden;
  }
}
</style>
