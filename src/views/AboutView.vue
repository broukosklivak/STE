<!--Stránka About-->
<template>
  
    <Header title="About" />
    <div class="about">
    <h1>Basic information</h1>
    <p>This application is a simple text editor written in Vue.js. It uses VIM-like commands but it still can style
    text.</p>
    <h2>How to use the editor</h2>
    <p>All the functions of the editor will be described here, but you will learn how to use it better in the <router-link to="/tutor">tutorial</router-link></p>
    <ol>
      <li>
        <h3>Normal and insert mode</h3>
        <p>Thereis normal and insert mode in the editor. In normal mode you can move around the text and execute commands, in insert mode you insert text. To switch to insert mode 
          press the i key, to switch to normal mode press the Esc key.
        </p>
      </li>
      <li>
        <h3>Executing the commands</h3>
        <p>You can execute the command only in normal mode. Press the Tab key and write the command. Some commands need additional values, after you write the command editor will ask you
          for the additional value. Select value from the list and write it in the prompt (the value needs to be 100% accurate, otherwise the command will not be executed).
        </p>
      </li>
      <li>
        <h3>Deleting</h3>
        <ol>
          <li>
            <h3>Deleting one character</h3>
            <p>In normal mode move the cursor before the character that you want delete then press the x key to delete the character.</p>
          </li>
          <li>
            <h3>Deleting one word</h3>
            <p>In normal mode move your cursor in word that you want to delete and execute the "dw" command to delete
              the word.
            </p>
          </li>
          <li>
            <h3>Deleting one line</h3>
            <p>In normal mode move your cursor on line that you want to delete and execute the "dd" command to delete 
              the line.
            </p>
          </li>
          <li>
            <h3>Deleting whole document / creating new document</h3>
            <p>In normal mode execute the "qa" command to delete whole document / create new document.</p>
          </li>
        </ol>
      </li>
      <li>
        <h3>Styling the text</h3>
        <ol>
          <li>
          <h3>Making the text bold</h3>
          <p>Select text and click on <i class="fa fa-bold fa-fw"></i> icon or execute the "bold" command to make the selected text <b>bold</b>.</p>
          </li>
          <li>
          <h3>Making the text italic</h3>
          <p>Select the text and click on <i class="fa fa-italic fa-fw"></i> icon or execute the "italic" command to make the selected text <i>italic</i>.</p>
          </li>
          <li>
          <h3>Creating the lists</h3>
          <p>Click on <i class="fa fa-list fa-fw"></i> icon or execute the "list" command to create the unordered list. Click on <i class="fa fa-list-ol"></i> or execute the "listol"
          command to create the ordered list.</p>
          </li>
          <li>
          <h3>Changing the text size</h3>
          <p>Select the text and choose text size in the panel next to this icon <i class="fa-solid fa-text-height"></i>
          or execute the "textsize" command to change size of the selected text.</p>
          </li>
          <li>
          <h3>Changing the font</h3>
          <p>Select the text and choose text size in the panel next to this icon <i class="fa-solid fa-font"></i> or execute the "font" command to change font of the selected text.</p>
          </li>
          <li>
          <h3>Changing the text color</h3>
          <p>Select the text and choose text size in the panel next to this icon <i class="fa-solid fa-brush"></i> or execute the "textcolor" command to change color of selected text.</p>
          </li>
          <li>
          <h3>Highlighting the text</h3>
          <p>Select the text and choose text size in the panel next to this icon <i class="fa-solid fa-fill"></i> or execute the "backcolor" command to change color of selected text.</p>
          </li>
          <li>
          <h3>Aligning the text</h3>
          <p>Select the text and click on <i class="fa-solid fa-align-left"></i> icon or execute the "alignleft" 
          command to aligning the text to left</p>
          <p align="center">Select the text and click on <i class="fa-solid fa-align-center"></i> icon or execute the "aligncenter" 
          command to aligning the text to center</p>
          <p align="right">Select the text and click on <i class="fa-solid fa-align-right"></i> icon or execute the "alignright" 
          command to aligning the text to right</p>
          </li>
          <li>
          <h3>Underlining the text</h3>
          <p>Select the text and click on <i class="fa-solid fa-underline"></i> icon or execute the "underline"
          command to <u>underline</u> selected text</p>
          </li>
          <li>
          <h3>Subscripting the text</h3>
          <p>Select the text and click on <i class="fa-solid fa-subscript"></i> icon or execute the "subscript" 
          command to <sub>subscript</sub> the selected text.</p>
          </li>
          <li>
          <h3>Superbscripting the text</h3>
          <p>Select the text and click on <i class="fa-solid fa-superscript"></i> icon or execute the "superscript" 
          command to <sup>superscript</sup> the selected text.</p>
          </li>
          <li>
          <h3>Creating the hyperlink</h3>
          <p>Select the text and click on <i class="fa-solid fa-link"></i> icon or execute the "link" command to
          create a <a>hyperlink</a> from the selected text.</p>
          </li>
          <li>
          <h3>Unlinking the hyperlink</h3>
          <p>Select the text and click on <i class="fa-solid fa-link-slash"></i> icon or execute the "unlink" command to
          unlink the selected hyperlink.</p>
          </li>
        </ol>
      </li>
      <li>
        <h3>Images</h3>
        <p>Select a source of your image in panel next to <i class="fa-solid fa-image"></i> icon then click on it
        or execute the "image" command. If you selected the URL as your source put your URL in the prompt. Else
        select the image file from your computer. Then put the width size of image that you want in the prompt
        or leave it empty if you want use original or maximal size of the image.
        </p>
      </li>
      <li>
        <h3>Undo / Redo</h3>
        <p>Click on <i class="fa-solid fa-rotate-left"></i> icon or use the "CTRL + Z" shortcut to undo last changes.
        Or click on <i class="fa-solid fa-rotate-right"></i> or use the "CTRL + SHIFT + Z" shortcut to redo last 
        undo command.</p>
      </li>
      <li>
        <h3>Save / Load</h3>
        <p>The editor automatically saves the document in your browser and loads it after every reload of the page.
        But if you want to use documents on your computer click on "Save file" button or execute the "wq" command to save
        the document. Or click on "Load file" button or execute the "dl" command and select the document from your computer
        to load the document.
        </p>
      </li>
    </ol>
  </div>
</template>

<script>
import Header from '../components/Header.vue'

export default {
  name: 'About',
  components: {
    Header
  }
}
</script>

<style scoped>
  .about{
    width: 1250px;
    margin: auto;
  }

  .about h1{
    font-size: 25px;
  }

  .about h2{
    margin-top: 50px;
    font-size: 25px;
  }

  .about h3{
    font-size: 22px;
  }

  .about li{
    font-size: 22px;
  }

  .about p{
    font-size: 18px;
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
