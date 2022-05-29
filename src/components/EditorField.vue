<template>
    <StylePanel @bold="format('bold')" @italic="format('italic')" @list="format('insertunorderedlist')" @list-ol="format('insertorderedlist')" @font-size="textSize" @font-name="font"
    @text-color="textColor" @back-color="backColor" @align-left="format('justifyLeft')" @align-center="format('justifyCenter')" @align-right="format('justifyRight')" 
    @subscript="format('subscript')" @superscript="format('superscript')" @link="link" @unlink="format('unlink')" @underline="format('underline')"
    @save="saveDocument()" @image="loadImage" @load="loadDocument()" @undo="format('undo')" 
    @redo="format('redo')" />
    <div class="editorfield" ref="editorfield" :insert="true" contenteditable="true" v-on:keydown="keyPress($event)"
    spellcheck="false" @click="focusLastLine">
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
            fileName: "New document",
            line: null
        }
    },

    mounted:function(){
        this.localStorageLoad()
        if (this.fileName == null){
            this.fileName = "New document"
        }
        this.$emit('insert', this.insert)
        if(this.$refs.editorfield.hasChildNodes()){
            this.$refs.editorfield.childNodes[0].focus()
        }
        this.lines() 
        this.line = document.activeElement
    },

    methods: {
    /*moveCaret(el, pos){

    // Loop through all child nodes
    for(var node of el.childNodes){
        if(node.nodeType == 3){ // we have a text node
            if(node.length >= pos){
                // finally add our range
            var sel;
            if(el.getSelection()){
                sel = el.getSelection()
                if(sel.rangeCount > 0){
                    var newOffset = sel.focusOffset + pos
                    sel.collapse(node, Math.min(node.length, newOffset))
                }
            }
            }
    }
    }
        this.line.focus()
    return pos; // needed because of recursion stuff
},*/

    arrow(keyCode){
        if(keyCode == 38){
            if(this.line.previousSibling == null){
                this.line.focus()
            }
                    else{
                    this.line.previousSibling.focus();
                    this.line = document.activeElement
                    }
        }
        else if(keyCode == 40){
            if(this.line.nextSibling == null){
                this.line.focus()
            }
                    else{
                    this.line.nextSibling.focus();
                    this.line = document.activeElement
                    }
        }
        else if(keyCode == 37 || 38){
            this.line.focus()
        }
    },

    enter(){
        this.lines()
        this.$refs.editorfield.lastChild.click()
    },

    focusLastLine() {
    if(this.$refs.editorfield.hasChildNodes()){
        if(document.activeElement.innerHTML == this.$refs.editorfield.innerHTML){
            this.$refs.editorfield.lastChild.focus()
            this.line = document.activeElement
        }
        else{
            this.line = document.activeElement
        }
    }
    else{
    }
    },

    lines() {
    var nodes = this.$refs.editorfield.childNodes
    for(var i=0; i<nodes.length; i++) {
         if (nodes[i].nodeName.toLowerCase() == 'div') {
        nodes[i].setAttribute("tabindex", 0)
         }
    }
    },

    loadImage(imagechoose){
    if(imagechoose == "url"){
        this.loadImageURL()
    }
    else if(imagechoose == "file"){
        this.loadImageFile()
    }
    this.localStorageSave()
    },

    loadImageFile(){
    this.line.focus()
     let input = document.createElement("input")
        var result

        document.body.appendChild(input)

        input.setAttribute("type", "file")
        input.setAttribute("accept", "image/*")

        input.click()
        input.addEventListener('change', () => {

        let files = input.files

        const file = files[0]
        let reader = new FileReader()
        reader.addEventListener('load', (event) => {
        result = reader.result;
        document.execCommand("insertimage", false, result)
        this.imageSize(result)
        });
        reader.readAsDataURL(file);
        })
    },

    loadImageURL(){
    this.line.focus()
    var image = prompt ("Paste or type a link")
    if (image == null || image == ""){
            alert("No url was set")
        }
        else{
    document.execCommand("insertimage", false, image)
    this.imageSize(image)
        }
    },

    imageSize(image){
    var img = document.querySelector("img[src='"+ image +"']")
    let width = prompt ("Please enter width of image (in px)")
    if (width == null || width == "")
    {}
    else{
    img.style.width = width + "px" 
    }
    img.style.maxWidth = "100%"
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
        input.setAttribute("accept", ".doc")

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
        this.lines()
        this.localStorageSave()
 
        };

        reader.onerror = (event) => alert(event.target.error.name);
 
        reader.readAsText(file);
        })
        document.body.removeChild(input)
    },

    delete(what){
        if (what == "all"){
            this.$refs.editorfield.innerHTML = ""
            this.$refs.editorfield.focus()
        }
        else if (what == "line"){
            var nodes = this.$refs.editorfield.childNodes
            if(nodes.length == 1){
            this.$refs.editorfield.innerHTML = "" 
            }
            else{
            let line = document.activeElement
            document.activeElement.previousSibling.focus()
            this.$refs.editorfield.removeChild(line)
            }
        }
        else if(what == "word"){
        var sel
        if (window.getSelection && (sel = window.getSelection()).modify) {
        sel.collapseToStart();
        sel.modify("move", "backward", "word");
        sel.modify("extend", "forward", "word"); 
        this.format('forwardDelete')
        this.line.focus()
    }
        }
        this.localStorageSave()
    },

        keyPress(e){
                if (e.keyCode == 38){
                    setTimeout(this.arrow, 10, 38)
                    this.line = document.activeElement
                }
                else if (e.keyCode == 40){
                    setTimeout(this.arrow, 10, 40)
                    this.line = document.activeElement
                }
                else if (e.keyCode == 37){
                    setTimeout(this.arrow, 10, 37)
                    this.line = document.activeElement
                }
                else if (e.keyCode == 39){
                    setTimeout(this.arrow, 10, 39)
                    this.line = document.activeElement
                }

            if (this.insert == true){
                if (e.ctrlKey && e.key === 'z') {
                this.format('undo')
                }
                else if (e.ctrlKey && e.shiftKey && e.key === 'z') {
                this.format('redo')
                }
                switch(e.keyCode){
                    case 73: 
                    e.preventDefault()
                    this.insert = false
                    this.$emit('insert', this.insert)
                    break;

                    case 72:
                    e.preventDefault() 
                    this.moveCaret(this.$refs.editorfield, -1)
                    break;

                    case 76:
                    e.preventDefault()
                    this.moveCaret(this.$refs.editorfield, +1)
                    break;

                    case 88:
                    e.preventDefault()
                    this.format('forwardDelete')
                    var previousLine = document.activeElement.previousSibling
                    var x = new MutationObserver(function (e) {
                    if (e[0].removedNodes){
                    previousLine.focus();
                    x.disconnect()
                    }
                    });
                    x.observe(this.$refs.editorfield, { childList: true });
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
                switch(e.keyCode){
                    case 27:
                    this.insert = true
                    this.$emit('insert', this.insert)
                    break;

                    case 13:
                    setTimeout(this.enter, 10)
                    break;

                    case 8:
                    var previousLine = document.activeElement.previousSibling
                    var x = new MutationObserver(function (e) {
                    if (e[0].removedNodes){
                    previousLine.focus();
                    x.disconnect()
                    }
                    });
                    x.observe(this.$refs.editorfield, { childList: true });
                    break;
                }
            }
        },

    format(command, value) {
    this.line.focus()
    document.execCommand(command, false, value)
    this.localStorageSave()
 },
    
    textSize(size){
        this.line.focus()
        document.execCommand("fontSize", false, size)
        this.localStorageSave()
    },

    font(fontName){
        this.line.focus()
        document.execCommand("fontName", false, fontName)
        this.localStorageSave()
    },

    textColor(textcolor){
        this.line.focus()
        document.execCommand("foreColor", false, textcolor)
        this.localStorageSave()
    },

    backColor(backcolor){
        this.line.focus()
        document.execCommand("backColor", false, backcolor)
        this.localStorageSave()
    },

    link(){
        this.line.focus()
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
        this.lines()
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
                        let imagechoose = prompt("Please enter source of image: (URL, File)")
                        if(imagechoose == "URL"){
                        imagechoose = "url"
                        }
                        else if(imagechoose == "File"){
                        imagechoose = "file"
                        }
                        else{
                        alert('This is not an option')
                        }
                        this.loadImage(imagechoose)
                        break;

                        case "dl":
                        this.loadDocument()
                        break;

                        case "dd":
                        this.delete("line")
                        break;

                        case "dw":
                        this.delete("word")
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