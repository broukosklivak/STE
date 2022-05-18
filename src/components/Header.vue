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
    }

  nav {
    font-size: 32px;    
  }

  a{
    text-decoration: none;
    color: black;
  }

  a:hover{
    color: darkslategray;
  }

  a:active{
    color: darkgray;
  }
</style>