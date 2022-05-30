<!--Component pro footer-->
<template>
    <footer>
    <p>Tomáš Bártek | Simple text editor | 2022</p>
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
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    }
</style>