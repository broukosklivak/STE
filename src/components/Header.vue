<template>
    <header>
        <h1>{{ title }}</h1>
        <nav>
        <router-link to="/">Editor</router-link> |
        <router-link to="/about">About</router-link>
        </nav>
    </header>
</template>

<script>
export default {
    name: 'Header',
    props: {
        title: String,
    }
}
</script>

<style scoped>
    header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-left: 50px;
        margin-right: 50px;
        -webkit-touch-callout: none;
        -webkit-user-select: none;
        -khtml-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
    }

  nav {
    font-size: 32px;    
  }

  a{
    text-decoration: none;
    color: black;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
  }

  a:hover{
    color: darkslategray;
  }

  a:active{
    color: darkgray;
  }
</style>