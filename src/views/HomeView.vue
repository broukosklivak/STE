<template>
  <div class="home"  v-on:keydown="command($event)">
    <Header title="Simple text editor" />
    <EditorField ref="editorfield" @insert="insert"/>
  </div>
</template>

<script>
import EditorField from '../components/EditorField.vue'
import Header from '../components/Header.vue'

export default {
  name: 'HomeView',
  components: {
    EditorField,
    Header,
  },

  data(){
    return{
      insertvar: true
    }
  },

  methods: {
    command(e){ 
      if (e.keyCode == 9){
        if (this.insertvar == true){
          e.preventDefault()
          this.$refs.editorfield.commands()
        }
        else{}
      }
    },

    insert(value){
      this.insertvar = value
    }
  }
}
</script>