<template>
  <div id="app">
    <router-view />
  </div>
</template>

<style lang="scss">

  * {
    box-sizing: border-box;
  }

  body, html {
    display: grid;
    margin: 0;
    min-height: 100vh;
  }

  #app {
    font-family: sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #333;
    background-color: #f0f0f0;

    display: grid;
  }
</style>
