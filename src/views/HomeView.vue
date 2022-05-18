<template>
  <div class="home">
    <Header title="Simple text editor" />
    <EditorField />
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
  }
}
</script>