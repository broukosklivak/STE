<!--Domovská stránka (samotný editor)-->
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
      //Boolean který určuje jestli je editor v normal nebo insert modu
      insertvar: true
    }
  },

  methods: {
    /*Metoda která spustí metodu commands() v componentu editorfield. Tato metoda je potřebná k tomu, aby uživatel
    mohl spouštět příkazy i když není zrovna pole editoru aktivní*/
    command(e){ 
      if (e.keyCode == 9){
        if (this.insertvar == true){
          e.preventDefault()
          this.$refs.editorfield.commands()
        }
        else{}
      }
    },

    //Metoda která nastavuje hodnotu proměnné insertval
    insert(value){
      this.insertvar = value
    }
  }
}
</script>