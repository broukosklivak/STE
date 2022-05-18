<template>
<div class="tutor">
    <Header title="Tutorial (WIP)" />
    <Tutor />
</div>    
</template>
<script>
import Header from '../components/Header.vue'
import Tutor from '../components/Tutor.vue'

export default {
    name: 'TutorView',
    components:{
        Header,
        Tutor
    }
}
</script>