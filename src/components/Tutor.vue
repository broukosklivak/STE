<template>
    <StylePanel @bold="format('bold')" @italic="format('italic')" @list="format('insertunorderedlist')" @list-ol="format('insertorderedlist')" @font-size="textSize" @font-name="font"
    @text-color="textColor" @back-color="backColor" @align-left="format('justifyLeft')" @align-center="format('justifyCenter')" @align-right="format('justifyRight')" 
    @subscript="format('subscript')" @superscript="format('superscript')" @link="link" @unlink="format('unlink')" @underline="format('underline')" />
    <div class="editorfield" :insert="true" contenteditable="true" v-on:keydown="keyPress($event)"
    spellcheck="false">
    <div>
        This is tutorial of the editor. In this tutorial you can try every function of the editor.
    </div>
    <div>
        <br/>
    </div>
    <div>
        Try to switching between normal and insert modes. Press i key to switch to insert mode and press Esc key to switch normal mode.
    </div>
    <div></div>
    <div>
        Use insert mode to make lines below the same.
    </div>
    <div><br/></div>
    <div>
        There is some text missing from this line.
    </div>
    <div>
        There is text misng this .
    </div>
    <div>
        <br/>
    </div>
    <div>
        Try to execute the command. Press Tab key to open command line and write in the command.
    </div>
    <div></div>
    <div>
        Use the "textcolor" command to make lines below same color.
    </div>
    <div>
        <br/>
    </div>
    <div>
        <font color="#0000ff">This text is blue.</font>
    </div>
    <div></div>
    <div>
        This text is blue.
    </div>
    <div><br/></div>
    <div>
        Try to correct mistakes in normal mode. Move your cursor before the character that you want delete and press x key.
    </div>
    <div></div>
    <div>
        Use delete function to fix the sentence below.
    </div>
    <div><br/></div>
    <div>
        The ccow jumpedd ovverr thhe mooon.
    </div>
    <div><br/></div>
    <div>
        Now let's try styling the text.
    </div>
    <div></div>
    <div>
        Select the text in line below and click on this <i class="fa fa-bold fa-fw"></i> icon or execute the "bold"
        command to make the selected text <b>bold</b>.
    </div>
    <div><br/></div>
    <div>This text is bold.</div>
    </div>
</template>

<script>
import StylePanel from '../components/StylePanel.vue'

export default {
    name: 'EditorField',

    components:{
        StylePanel
    },

    data(){
        return{
            insert: true
        }
    },

    methods: {
    moveCaret(win, charCount) {
    var sel;
    if (win.getSelection) {
        sel = win.getSelection();
        if (sel.rangeCount > 0) {
            var textNode = sel.focusNode;
            var newOffset = sel.focusOffset + charCount;
            sel.collapse(textNode, Math.min(textNode.length, newOffset));
        }
    } 
    },

        keyPress(e){
            if (this.insert == true){
                switch(e.keyCode){
                    case 73: 
                    e.preventDefault()
                    alert("Insert mode")
                    this.insert = false
                    break;

                    case 72:
                    e.preventDefault() 
                    this.moveCaret(window, -1)
                    break;

                    case 76:
                    e.preventDefault()
                    this.moveCaret(window, 1)
                    break;

                    case 88:
                    e.preventDefault()
                    this.format('forwardDelete')
                    break;

                    case 9:
                    e.preventDefault()
                    let command = prompt("Write the command here:")
                    switch(command){
                        case "bold":
                        this.format('bold')
                        break;

                        case "italic":
                        this.format('italic')
                        break;
                    
                        case "list":
                        this.format('insertunorderedlist')
                        break;

                        case "textsize":
                        let size = prompt("Write the text size here (1-7):")
                        if (size < 1 || size > 7){
                            alert("This is not an option!")
                        }
                        else{
                            this.textSize(size)
                        }
                        break;

                        case "font":
                        let fontName = prompt("Write the font here (Arial, Helvetica, Times New Roman, Courier New, Archivo Narrow, Roboto):")
                        if (fontName == "Arial" || fontName == "Helvetica" || fontName == "Times New Roman" || fontName == "Courier New" || fontName == "Archivo Narrow" || 
                        fontName == "Roboto"){
                            this.font(fontName)
                        }
                        else{
                            alert("This is not an option!")
                        }
                        break;

                        case "listol":
                        this.format('insertorderedlist')
                        break;

                        case "textcolor":
                        let colorname = prompt("Write the color here (Yellow, Green, Blue, Violet, Red, Orange, Black, Gray, Brown, White):")
                        this.colorSwitch(colorname, true)
                        break;

                        case "backcolor":
                        let backcolorname = prompt("Write the color here (Yellow, Green, Blue, Violet, Red, Orange, Black, Gray, Brown, White):")
                        this.colorSwitch(backcolorname, false)
                        break;

                        case "alignleft":
                        this.format('justifyLeft')
                        break;

                        case "aligncenter":
                        this.format('justifyCenter')
                        break;

                        case "alignright":
                        this.format('justifyRight')
                        break;

                        case "subscript":
                        this.format('subscript')
                        break;

                        case "superscript":
                        this.format('superscript')
                        break;

                        case "link":
                        this.link()
                        break;

                        case "unlink":
                        this.format('unlink')
                        break;

                        case "underline":
                        this.format('underline')
                        break;

                        default:
                        alert("This is not a command!")
                    }

                    case 37:
                    break;

                    case 38:
                    break;

                    case 39:
                    break;

                    case 40:
                    break;

                    default: 
                    e.preventDefault()
                }
            }
            else if (this.insert == false){
                if (e.keyCode == 27){
                    alert("Normal mode")
                    this.insert = true
                }
            }
        },

    format(command, value) {
    document.execCommand(command, false, value)
 },
    
    textSize(size){
        document.execCommand("fontSize", false, size)
    },

    font(fontName){
        document.execCommand("fontName", false, fontName)
    },

    textColor(textcolor){
        document.execCommand("foreColor", false, textcolor)
    },

    backColor(backcolor){
        document.execCommand("backColor", false, backcolor)
    },

    link(){
        let url = prompt("Write the URL here:")
        this.format('createLink', url)
    },

    colorSwitch(colorname, textcolor){
        let colorcode

            switch(colorname){
                case "Yellow":
                colorcode = "ffff00"
                break;

                case "Green":
                colorcode = "#008000"
                break;

                case "Blue":
                colorcode = "#0000ff"
                break;

                case "Violet":
                colorcode = "#ee82ee"
                break;

                case "Red":
                colorcode = "#ff0000"
                break;

                case "Orange":
                colorcode = "#ffa500"
                break;

                case "Black":
                colorcode = "#000000"
                break;

                case "Gray":
                colorcode = "#808080"
                break;

                case "Brown":
                colorcode = "#8b4513"
                break;

                case "White":
                colorcode = "#ffffff"
                break;

                default: 
                alert("This is not an option!")
                }
            if (textcolor == true){    
            this.textColor(colorcode)
            }
            else if (textcolor == false){
            this.backColor(colorcode)    
            }
    },
       }
    }
</script>

<style scoped>
    .editorfield{
        padding: 5px;
        display: block;
        margin: auto;
        resize: none;
        outline: none;
        border: 2px solid black;
        width: 1250px;
        height: 1000px;
        word-wrap: break-word;
    }
</style>