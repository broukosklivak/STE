<template>
    <footer>
    <p>Tomáš Bártek | Simple text editor | 2022</p>
    <router-link to="/tutor">Tutorial</router-link>
    </footer>
</template>

<script>
export default {
    name: 'Footer',
}
</script>

<style scoped>
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

    footer{
    text-align: center;
    }
</style>