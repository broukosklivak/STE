<!--Panel který spouští funkce pro formátování textu. Po kliknutí na tlačítko, nebo vybrání hodnoty odešle informaci
o stisknutí tlačítka do nadřazeného componentu-->
<template>
    <div class="stylepanel">
    <span class="fa fa-bold fa-fw" aria-hidden="true" @click="$emit('bold')" title="Or 'bold' command"></span>
    <span class="fa fa-italic fa-fw" aria-hidden="true" @click="$emit('italic')" title="Or 'italic' command"></span>
    <span class="fa fa-list fa-fw" aria-hidden="true" @click="$emit('list')" title="Or 'list' command"></span>
    <span class="fa fa-list-ol" aria-hidden="true" @click="$emit('list-ol')" title="Or 'listol' command"></span>
    <i class="fa-solid fa-text-height" aria-hidden="true" title="Or 'textsize' command"></i>
    <input type="number" min="1" max="7" v-model="size" @change="$emit('font-size', this.size)" class="selectsize">
    <i class="fa-solid fa-font" aria-hidden="true" title="Or 'font' command"></i>
    <select v-model="fontName" @change="$emit('font-name', this.fontName)">
        <option value="Arial">Arial</option>
        <option value="Helvetica">Helvetica</option>
        <option value="Times New Roman">Times New Roman</option>
        <option value="Courier New">Courier New</option>
        <option value="Archivo Narrow" selected>Archivo Narrow</option>
        <option value="Roboto">Roboto</option>
    </select>
    <i class="fa-solid fa-brush" aria-hidden="true" title="Or 'textcolor' command"></i>
    <select v-model="textcolor" @change="$emit('text-color', this.textcolor)" >
        <option value="#ffff00">Yellow</option>
        <option value="#008000">Green</option>
        <option value="#0000ff">Blue</option>
        <option value="#ee82ee">Violet</option>
        <option value="#ff0000">Red</option>
        <option value="#ffa500">Orange</option>
        <option value="#000000" selected>Black</option>
        <option value="#808080">Gray</option>
        <option value="#8b4513">Brown</option>
        <option value="#ffffff">White</option>
    </select>
    <i class="fa-solid fa-fill" aria-hidden="true" title="Or 'backcolor' command"></i>
    <select v-model="backcolor" @change="$emit('back-color', this.backcolor)" >
        <option value="#ffff00">Yellow</option>
        <option value="#008000">Green</option>
        <option value="#0000ff">Blue</option>
        <option value="#ee82ee">Violet</option>
        <option value="#ff0000">Red</option>
        <option value="#ffa500">Orange</option>
        <option value="#000000">Black</option>
        <option value="#808080">Gray</option>
        <option value="#8b4513">Brown</option>
        <option value="#ffffff" selected>White</option>
    </select>
    <span class="fa-solid fa-align-left" aria-hidden="true" @click="$emit('align-left') " title="Or 'alignleft' command"></span>
    <span class="fa-solid fa-align-center" aria-hidden="true" @click="$emit('align-center')" title="Or 'aligncenter' command"></span>
    <span class="fa-solid fa-align-right" aria-hidden="true" @click="$emit('align-right')" title="Or 'alignright' command"></span>
    <span class="fa-solid fa-underline" aria-hidden="true" @click="$emit('underline')" title="Or 'undrline' command"></span>
    <span class="fa-solid fa-subscript" aria-hidden="true" @click="$emit('subscript')" title="Or 'subscript' command"></span>
    <span class="fa-solid fa-superscript" aria-hidden="true" @click="$emit('superscript')" title="Or 'superscript' command"></span>
    <span class="fa-solid fa-link" aria-hidden="true" @click="$emit('link')" title="Or 'link' command"></span>
    <span class="fa-solid fa-link-slash" aria-hidden="true" @click="$emit('unlink')" title="Or 'unlink' command"></span>
    <span class="fa-solid fa-image" aria-hidden="true" @click="$emit('image', this.imagechoose)" title="Or 'image' command"></span>
    <select v-model="imagechoose">
        <option value="url" selected>URL</option>
        <option value="file">File</option>
    </select>
    <span class="fa-solid fa-rotate-left" aria-hidden="true" @click="$emit('undo')" title="Or 'CTRL+Z' shortcut"></span>
    <span class="fa-solid fa-rotate-right" aria-hidden="true" @click="$emit('redo')" title="Or 'CTRL+SHIFT+Z' shortcut"></span>
            <button class="save" @click="$emit('save')" title="Or 'wq' command">Save file</button>
            <button @click="$emit('load')" title="Or 'dl' command">Load file</button>
    </div>
</template>

<script>
export default {
    name: 'StylePanel',

    data(){
        return{
            //Proměnná pro velikost písma
            size: 3,
            //Proměnná pro jméno fontu písma
            fontName: "Archivo Narrow",
            //Proměnná pro barvu písma
            textcolor: "Black",
            //Proměnná pro barvu zvýraznění
            backcolor: "White",
            //Proměnná pro název zdroje obrázku
            imagechoose: "url"
        }
    }
}
</script>

<style scoped>
    .stylepanel
    {
    border:solid 1px #ddd;
    background:#f4f4f4;
    padding: 5px;
    border-radius:3px;
    width: 1250px;
    margin: auto;
    margin-bottom: 20px;
    color: black;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    }

    .stylepanel > span
    {
    cursor:pointer;
    margin-left: 5px;
    }

    .stylepanel > span:hover
    {
    color: darkslategray;
    }

    .stylepanel > span:active
    {
    color: darkgray; 
    }

    .stylepanel > i
    {
    margin-left: 5px;
    }

    .save
    {
    margin-left: 250px;
    }

    .selectsize{
        width: 30px;
        outline: none;
    }
</style>