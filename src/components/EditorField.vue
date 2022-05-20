<template>
    <StylePanel @bold="format('bold')" @italic="format('italic')" @list="format('insertunorderedlist')" @list-ol="format('insertorderedlist')" @font-size="textSize" @font-name="font"
    @text-color="textColor" @back-color="backColor" @align-left="format('justifyLeft')" @align-center="format('justifyCenter')" @align-right="format('justifyRight')" 
    @subscript="format('subscript')" @superscript="format('superscript')" @link="link" @unlink="format('unlink')" @underline="format('underline')"
    @save="saveDocument()" @image="loadImage()" @load="loadDocument()" />
    <div class="editorfield" ref="editorfield" :insert="true" contenteditable="true" v-on:keydown="keyPress($event)"
    spellcheck="false"> 
    </div>
    <BottomPanel :insert ="insert" :fileName="fileName" />
</template>

<script>
import StylePanel from '../components/StylePanel.vue'
import BottomPanel from '../components/BottomPanel.vue'

export default {
    name: 'EditorField',

    components:{
        StylePanel,
        BottomPanel
    },

    data(){
        return{
            insert: true,
            fileName: "New document"
        }
    },

    mounted:function(){
        this.localStorageLoad()
        if (this.fileName == null){
            this.fileName = "New document"
        }
        this.$emit('insert', this.insert)
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

    loadImage(){
    var image = prompt ("Paste or type a link")
    if (image == null || image == ""){
            alert("No url was set")
        }
        else{
    document.execCommand("insertimage", false, image)

    var img = document.querySelector("img[src='"+ image +"']")
    let width = prompt ("Please enter width of image (in px)")
    if (width == null || width == "")
    {}
    else{
    img.style.width = width + "px" 
    }
    img.style.maxWidth = "100%"
        }
    this.localStorageSave()
    },

    saveDocument(){
    var preHtml = "";
    var postHtml = "";
    var html = preHtml+this.$refs.editorfield.innerHTML+postHtml;

    var blob = new Blob(['\ufeff', html], {
        type: 'application/msword'
    });
    
    // Specify link url
    var url = 'data:application/vnd.ms-word;charset=utf-8,' + encodeURIComponent(html);
    
    // Specify file name
    let nameprompt

    if (this.fileName == "New document"){
        nameprompt = prompt("Please enter file name")
    if (nameprompt == null || nameprompt == ""){
        nameprompt = "Document"
    }
    }
    else{
        nameprompt = this.fileName
    }

    let filename

        if (nameprompt == this.fileName){
            filename = nameprompt
        }
        else{
            filename = nameprompt+'.doc'
        }
    
    // Create download link element
    var downloadLink = document.createElement("a");

    document.body.appendChild(downloadLink);
    
    if(navigator.msSaveOrOpenBlob ){
        navigator.msSaveOrOpenBlob(blob, filename);
    }else{
        // Create a link to the file
        downloadLink.href = url;
        
        // Setting the file name
        downloadLink.download = filename;
        
        //triggering the function
        downloadLink.click();
        this.fileName = filename
    }
    
    document.body.removeChild(downloadLink);
    this.localStorageSave()
    },

    loadDocument(){
        let input = document.createElement("input")
        let editorfield = this.$refs.editorfield
        var result

        document.body.appendChild(input)

        input.setAttribute("type", "file")

        input.click()

        input.addEventListener('change', () => {

        let files = input.files

        const file = files[0]

        var fname = file.name

        let reader = new FileReader()
 
        reader.onload = (event) => {
        const file = event.target.result;

        editorfield.innerHTML = file
 
        const lines = file.split(/\r\n|\n/);
        editorfield.value = lines.join('\n');

        result = reader.result

        this.fileName = fname
        this.localStorageSave()
        console.log(JSON.parse(window.localStorage.getItem('field')))
 
        };

        reader.onerror = (event) => alert(event.target.error.name);
 
        reader.readAsText(file);
        })
        document.body.removeChild(input)
    },

    delete(what){
        if (what == "all"){
            this.$refs.editorfield.innerHTML = ""
        }
    },

        keyPress(e){
            if (this.insert == true){
                switch(e.keyCode){
                    case 73: 
                    e.preventDefault()
                    this.insert = false
                    this.$emit('insert', this.insert)
                    break;

                    case 72:
                    e.preventDefault() 
                    this.moveCaret(window, -1)
                    break;

                    case 76:
                    e.preventDefault()
                    this.moveCaret(window, +1)
                    break;

                    case 88:
                    e.preventDefault()
                    this.format('forwardDelete')
                    break;

                    case 9:
                    e.preventDefault()
                    break;

                    case 37:
                    break;

                    case 38:
                    break;

                    case 39:
                    break;

                    case 40:
                    break;

                    case 116:
                    break;

                    default: 
                    e.preventDefault()
                }
            }
            else if (this.insert == false){
                this.localStorageSave()
                if (e.keyCode == 27){
                    this.insert = true
                    this.$emit('insert', this.insert)
                }
            }
        },

    format(command, value) {
    document.execCommand(command, false, value)
    this.localStorageSave()
 },
    
    textSize(size){
        document.execCommand("fontSize", false, size)
        this.localStorageSave()
    },

    font(fontName){
        document.execCommand("fontName", false, fontName)
        this.localStorageSave()
    },

    textColor(textcolor){
        document.execCommand("foreColor", false, textcolor)
        this.localStorageSave()
    },

    backColor(backcolor){
        document.execCommand("backColor", false, backcolor)
        this.localStorageSave()
    },

    link(){
        let url = prompt("Please enter URL")
        if (url == null || url == ""){
            alert("No url was set")
        }
        else{
        this.format('createLink', url)
        }
        this.localStorageSave()
    },

    colorSwitch(colorname, textcolor){
        let colorcode

            switch(colorname){
                case "Yellow":
                colorcode = "ffff00"
                break;

                case "yellow":
                colorcode = "ffff00"
                break;

                case "Green":
                colorcode = "#008000"
                break;

                case "green":
                colorcode = "#008000"
                break;

                case "Blue":
                colorcode = "#0000ff"
                break;

                case "blue":
                colorcode = "#0000ff"
                break;

                case "Violet":
                colorcode = "#ee82ee"
                break;

                case "violet":
                colorcode = "#ee82ee"
                break;

                case "Red":
                colorcode = "#ff0000"
                break;

                case "red":
                colorcode = "#ff0000"
                break;

                case "Orange":
                colorcode = "#ffa500"
                break;

                case "orange":
                colorcode = "#ffa500"
                break;

                case "Black":
                colorcode = "#000000"
                break;

                case "black":
                colorcode = "#000000"
                break;

                case "Gray":
                colorcode = "#808080"
                break;

                case "gray":
                colorcode = "#808080"
                break;

                case "Brown":
                colorcode = "#8b4513"
                break;

                case "brown":
                colorcode = "#8b4513"
                break;

                case "White":
                colorcode = "#ffffff"
                break;

                case "white":
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

    localStorageSave(){
        let field = this.$refs.editorfield.innerHTML
        window.localStorage.setItem('field', JSON.stringify(field))
        window.localStorage.setItem('name', JSON.stringify(this.fileName))
    },

    localStorageLoad(){
        let field = JSON.parse(window.localStorage.getItem('field'))
        this.$refs.editorfield.innerHTML = field
        this.fileName = JSON.parse(window.localStorage.getItem('name'))
    },
    
    commands(){
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

                        case "wq":
                        this.saveDocument()
                        break;

                        case "qa":
                        let what = "all"
                        this.delete(what)
                        this.fileName = "New document"
                        window.localStorage.clear()
                        this.localStorageSave()
                        break;

                        case "image":
                        this.loadImage()
                        break;

                        case "dl":
                        this.loadDocument()
                        break;

                        default:
                        alert("This is not a command!")
                    }
    }
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
        height: 450px;
        word-wrap: break-word;
        resize: vertical;
        overflow: auto;
    }
</style>