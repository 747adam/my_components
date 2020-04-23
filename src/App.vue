<template>
  <div id="app">
    <div id="nav">
      <router-link to="/index.html">
        Home
      </router-link> |
      <router-link to="/scrollDemo.html">
        大圖輪播
      </router-link> |
      <router-link to="/scrollNav.html">
        錨點選單
      </router-link>
    </div>
    <router-view />
  </div>
</template>

<style lang="scss">
@import '~@/assets/style/main';

#app {
  padding-top: 80px;
  font-size: 16px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  line-height: 20px;
}

a {
  text-decoration: none;
}

#nav {
  position: fixed;
  top: 0;
  left: 0;
  padding: 30px;
  width: 100%;
  background: #ffffff;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
