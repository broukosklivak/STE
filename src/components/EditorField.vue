<!--Component pro pole editoru-->
<template>
    <!--Naimportovaný component StylePanel a emity díky kterým se spouští metody po kliknutí na tlačítko.-->
    <StylePanel @bold="format('bold')" @italic="format('italic')" @list="format('insertunorderedlist')" @list-ol="format('insertorderedlist')" @font-size="textSize" @font-name="font"
    @text-color="textColor" @back-color="backColor" @align-left="format('justifyLeft')" @align-center="format('justifyCenter')" @align-right="format('justifyRight')" 
    @subscript="format('subscript')" @superscript="format('superscript')" @link="link" @unlink="format('unlink')" @underline="format('underline')"
    @save="saveDocument()" @image="loadImage" @load="loadDocument()" @undo="format('undo')" 
    @redo="format('redo')" />
    <div class="editorfield" ref="editorfield" :insert="true" contenteditable="true" v-on:keydown="keyPress($event)"
    spellcheck="false" @click="focusLastLine">
    </div>
    <!--Naimportovaný component BottomPanel do kterého se posílají proměnné insert a fileName.-->
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
            //Proměnná ve které je jestli je uživatel v normal nebo insert modu.
            insert: true,
            //Proměnná ve které je název dokumentu
            fileName: "New document",
            //Proměnná do které se ukládá aktivní řádek
            line: null
        }
    },
    //funkce která se spouští při každém načtení stránky 
    mounted:function(){
        //Načtení dat z localStorage
        this.localStorageLoad()
        //Pokud je název dokumentu po načtení z localStorage null změní se na "New document"
        if (this.fileName == null){
            this.fileName = "New document"
        }
        //Odeslání proměnné insert do podřazeného componentu
        this.$emit('insert', this.insert)
        //Pokud má pole editoru více řádků(tudíž má podřazené elementy) zafocusuje se první řádek 
        if(this.$refs.editorfield.hasChildNodes()){
            this.$refs.editorfield.childNodes[0].focus()
        }
        //Spuštění metody lines
        this.lines() 
        //Nastavení proměnné line na aktivní element
        this.line = document.activeElement
    },

    methods: {
    //Metoda která spravuje stisknutí kláves šipek
    arrow(keyCode){
        //Šipka nahoru
        if(keyCode == 38){
            //Pokud aktivní řádek nemá žádný předchozí sourozenecký element, zůstane focus na aktivním řádku
            if(this.line.previousSibling == null){
                this.line.focus()
            }
            /*Pokud aktivní řádek má předchozí sourozenecký element, přepne se focus na ten předchozí element. 
            Do proměnné line se uloží nový aktivní řádek*/
                    else{
                    this.line.previousSibling.focus();
                    this.line = document.activeElement
                    }
        }
        //Šipka dolů
        else if(keyCode == 40){
            //Pokud aktivní řádek nemá žádný další sourozenecký element, zůstane focus na aktivním řádku
            if(this.line.nextSibling == null){
                this.line.focus()
            }
            /*Pokud aktivní řádek má další sourozenecký element, přepne se focus na ten další element. 
            Do proměnné line se uloží nový aktivní řádek*/
                    else{
                    this.line.nextSibling.focus();
                    this.line = document.activeElement
                    }
        }
        //Šipky doprava a doleva
        else if(keyCode == 37 || 38){
            //Focus zůstane na aktivním řádku
            this.line.focus()
        }
    },

    //Metoda která spravuje stisknutí tlačítka enter
    enter(){
        //Spustí metodu lines a "klikne" na poslední podřazený element pole editoru (klasický focus nefungoval)
        this.lines()
        this.$refs.editorfield.lastChild.click()
    },

    //Metoda která při kliknutí na pole editoru určí na který řádek se přepne focus
    focusLastLine() {
    //Pole editoru má více řádku (tudíž má podřazené elementy)
    if(this.$refs.editorfield.hasChildNodes()){
        /*Pokud se po kliknutí innerHTML aktivního elementu rovná innerHTML pole editoru, znamená to že uživatel
        kliknul mimo nějaký vytvořený řádek*/ 
        if(document.activeElement.innerHTML == this.$refs.editorfield.innerHTML){
            //Focus se přepne na poslední řádek
            this.$refs.editorfield.lastChild.focus()
            //Proměnná line se přepíše na nově aktivní řádek
            this.line = document.activeElement
        }
        /*Pokud se po kliknutí innerHTML aktivního elementu nerovná innerHTML pole editoru, znamená to že uživatel
        kliknul na nějaký vytvořený řádek*/ 
        else{
            //Proměnná line se přepíše na aktivní element
            this.line = document.activeElement
        }
    }
    //Pokud pole editoru nemá více řádku (tudíž nemá podřazené elementy) neudělá metoda nic
    else{
    }
    },

    //Metoda která všem řádkům nastaví atribut tabindex = 0
    lines() {
    //Vytvoření proměnné pro všechny podřazené elementy pole editoru
    var nodes = this.$refs.editorfield.childNodes
    //forloop který spustí funkci na každém podřazeném elementu
    for(var i=0; i<nodes.length; i++) {
        //Podřazený element je div
         if (nodes[i].nodeName.toLowerCase() == 'div') {
        //Nastaví se u něj atribut tabindex = 0
        nodes[i].setAttribute("tabindex", 0)
         }
    }
    },

    /*Metoda která spouští jiné metody podle toho jaký zdroj pro vložení obrázku si uživatel vybral, k tomu používá
    proměnnou imagechoose*/
    loadImage(imagechoose){
    //Zdroj obrázku je URL adresa
    if(imagechoose == "url"){
        //Spustí se metoda loadImageURL
        this.loadImageURL()
    }
    //Zdroj obrázku je soubor v počítači
    else if(imagechoose == "file"){
        //Spustí se metoda loadImageFile
        this.loadImageFile()
    }
    //Nakonec se data pomocí metody localStorageSave uloží na localStorage
    this.localStorageSave()
    },

    //Metoda pro vložení obrázku ze souboru v počítači
    loadImageFile(){
    //Focus se přepne na aktivní řádek
    this.line.focus()
    //Vytvoření input elementu
     let input = document.createElement("input")
        //Vytvoření proměnné result
        var result

        document.body.appendChild(input)
        //Nastavení atributů pro input element
        input.setAttribute("type", "file")
        input.setAttribute("accept", "image/*")
        //"Kliknutí" na input element
        input.click()
        //Funkce která se spustí při zaznamenání změny v input elementu
        input.addEventListener('change', () => {
        //Proměnná jejíž hodnota jsou všechny soubory nahranné pomocí inputu
        let files = input.files
        /*Proměnná jejíž hodnota je první soubor nahranný pomocí inputu (jelikož nahráváme pouze jeden obrázek najednou)
        je to také jediný soubor nahraný pomocí inputu*/
        const file = files[0]
        //Vytvoření FileReaderu
        let reader = new FileReader()
        //Funkce která se spustí po načtení souboru do FileReaderu
        reader.addEventListener('load', (event) => {
        //Proměnná result jejíž hodnotou je výsledek readeru (tato proměnná se později použíje jako URL adresa obrázku)
        result = reader.result;
        //Samotné vytvoření obrázku
        document.execCommand("insertimage", false, result)
        //Spuštění metody imageSize do které se posílá proměnná result
        this.imageSize(result)
        });
        //FileReader přečte soubor jako DataUrl
        reader.readAsDataURL(file);
        })
        //Odstranění elementu insert
        document.body.removeChild(input)
    },

    //Metoda pro vložení obrázku z URL adresy
    loadImageURL(){
    //Focus se přepne na aktivní řádek
    this.line.focus()
    //Výzva k vložení URL adresy
    var image = prompt ("Paste or type a link")
    //Pokud je URL adresa prázdná zobrazí se chybová hláška
    if (image == null || image == ""){
            alert("No url was set")
        }
        /*Pokud URL adresa prázdná není vytvoří se obrázek a spustí se metoda imageSize a posílá se do ní proměnná
        ve které je uložená URL adresa*/
        else{
    document.execCommand("insertimage", false, image)
    this.imageSize(image)
        }
    },

    /*Metoda která nastaví základní šířku obrázku, pro výběr obrázku se používá proměnná image ve které je uložena
    URL adresa obrázku*/
    imageSize(image){
    //Proměnná do které se pomocí URL adresy obrázku uloží element vloženého obrázku
    var img = document.querySelector("img[src='"+ image +"']")
    //Výzva napojená na proměnnou width do které uživatel zadá požadovanou šířku obrázku, nebo pole nechá prázdné
    let width = prompt ("Please enter width of image (in px), or leave it empty if you want to use orignal size of image")
    //Pokud je proměnná width prázdná, zůstanou rozměry obrázku původní
    if (width == null || width == "")
    {}
    //Pokud je však nějaká šířka v proměnné width zadaná, nastaví se šířka obrázu na tu vloženou
    else{
    img.style.width = width + "px" 
    }
    //Nastavení maximální šířky, na šířku pole editoru
    img.style.maxWidth = "100%"
    //Spuštění metody localStorageSave
    this.localStorageSave()
    },

    //Metoda pro uložení dokumentu do počítače
    saveDocument(){
    //Vytvoření proměnné html, ve které je uložen element pole editoru a později se z něj vytvoří blob
    var preHtml = ""
    var postHtml = ""
    var html = preHtml+this.$refs.editorfield.innerHTML+postHtml
    //Blob typu aplikace MS Word vytvořen z proměnné html
    var blob = new Blob(['\ufeff', html], {
        type: 'application/msword'
    })
    
    //Vytvoření URL adresy z proměnné html, která slouží ke stažení dokumentu
    var url = 'data:application/vnd.ms-word;charset=utf-8,' + encodeURIComponent(html)
    
    //Vytvoření proměnné nameprompt která slouží jako pomocná proměnná k vytvoření názvu dokumentu
    let nameprompt
    /*Pokud je název dokumentu "New document" tzn. uživatel ukládá nový dokument, uživatel je vyzván k vepsání
    názvu dokumentu, který se uloží do proměnné nameprompt*/
    if (this.fileName == "New document"){
        nameprompt = prompt("Please enter file name")
    /*Pokud uživatel do pole nezadá žádný název tzn. proměnná nameprompt zůstane prázdná, uloží se do proměnné
    nameprompt hodnota "Document"*/
    if (nameprompt == null || nameprompt == ""){
        nameprompt = "Document"
    }
    }
    /*Pokud je název dokumentu jiný než "New document" tzn. uživatel ukládá vložený dokument, nebo dříve uložený 
    dokument, uloží se název dokumentu do proměnné nameprompt*/
    else{
        nameprompt = this.fileName
    }
    //Vytvoření pomocné proměnné pro vytvoření finálního názvu dokumentu
    let filename
        /*Pokud je proměnná nameprompt stejná jako původní název dokumentu, uloží se do proměnné filename hodnota
        proměnné nameprompt*/
        if (nameprompt == this.fileName){
            filename = nameprompt
        }
        /*Pokud se proměnná nameprompt liší od původního názvu dokumentu připíše se k ní přípona ".doc" a uloží se
        do proměnné filename*/
        else{
            filename = nameprompt+'.doc'
        }
    
    //Vytvoření elementu odkazu pro stažení dokumentu
    var downloadLink = document.createElement("a")

    document.body.appendChild(downloadLink)
    
    //Uložení dokumentu pomocí vestavěné funkce blobu (tato možnost není vždy funkční, proto je zde i druhá možnost)
    if(navigator.msSaveOrOpenBlob ){
        navigator.msSaveOrOpenBlob(blob, filename)
    //Uložení dokumentu pomocí elementu odkazu
    }else{
        //Přidání URL adresy z proměnné url do elementu odkazu
        downloadLink.href = url
        
        //Přidání názvu dokumentu z proměnné filename do elementu odkazu
        downloadLink.download = filename
        
        /*"Kliknutí" na element odkazu a nastavení proměnné názvu dokumentu kterou používá component bottompanel
        na hodnotu proměnné filename*/
        downloadLink.click()
        this.fileName = filename
    }
    //Odstranění elementu odkazu a spuštění metody localStorageSave
    document.body.removeChild(downloadLink)
    this.localStorageSave()
    },

    //Metoda pro načtení dokumentu
    loadDocument(){
        //Vytvoření input elementu
        let input = document.createElement("input")
        //Vytvoření proměnné s hodnotou pole editoru
        let editorfield = this.$refs.editorfield
        //Vytvoření pomocné proměnné result
        var result

        document.body.appendChild(input)
        //Nastavení atributů pro input element
        input.setAttribute("type", "file")
        input.setAttribute("accept", ".doc")
        //"Kliknutí" na input element
        input.click()
        //Funkce která se spustí při zaznamenání změny v input elementu
        input.addEventListener('change', () => {
        //Proměnná jejíž hodnota jsou všechny soubory nahranné pomocí inputu
        let files = input.files
        /*Proměnná jejíž hodnota je první soubor nahranný pomocí inputu (jelikož nahráváme pouze jeden dokument najednou)
        je to také jediný soubor nahraný pomocí inputu*/
        const file = files[0]
        //Proměnná fname jejíž hodnota je název nahraného souboru
        var fname = file.name
        //Vytvoření FileReaderu
        let reader = new FileReader()
        //Funkce která se spustí po načtení souboru do FileReaderu
        reader.onload = (event) => {
        //Proměná file jejíž hodnota je nahraný soubor
        const file = event.target.result
        //Použití proměnné file jako innerHTML pole editoru
        editorfield.innerHTML = file
        //Nastavení rozdělení řádků
        const lines = file.split(/\r\n|\n/)
        editorfield.value = lines.join('\n')
        //Použití hodnoty FileReadru jako hodnotu proměnné result
        result = reader.result
        /*Nastavení proměnné FileName (posílá se do componentu BottomPanel a spuštění metod lines a 
        localStorageSave*/
        this.fileName = fname
        this.lines()
        this.localStorageSave()
 
        }

        //Funkce pro zobrazení erroru FileReaderu
        reader.onerror = (event) => alert(event.target.error.name);
        //FileReader přečte soubor jako text
        reader.readAsText(file);
        })
        //Odstranění input elementu
        document.body.removeChild(input)
    },

    /*Metoda pro odstraňování textu. Informace o tom jakou část textu chce uživatel odstranit se dostává do metody
    pomocí proměnné what*/
    delete(what){
        //Uživatel chce odstranit celý dokument
        if (what == "all"){
            //innerHTML pole editoru se nastaví jako prázdné a na pole editoru se přepne focus
            this.$refs.editorfield.innerHTML = ""
            this.$refs.editorfield.focus()
        }
        //Uživatel chce odstranit celý řádek
        else if (what == "line"){
            /*Pokud pole editoru nemá víc než jeden podřazený element tzn. má pouze jeden řádek nastaví se innerHTML
            pole editoru jako prázdné*/
            var nodes = this.$refs.editorfield.childNodes
            if(nodes.length == 1){
            this.$refs.editorfield.innerHTML = "" 
            }
            //Pokud pole editoru má víc než jeden podřazený element tzn. má víc než jeden řádek
            else{
            //Nastaví se současný aktivní element jako proměnná line
            let line = document.activeElement
            //Pokud současný aktivní element nemá předchozí sourozenecký element
            if(document.activeElement.previousSibling == null){
                //Přepne se focus na další sourozenecký element
                document.activeElement.nextSibling.focus()
            }
            //Pokud současný aktivní element má předchozí sourozenecký element
            else{
                //Přepne se focus na předchozí sourozenecký element
                document.activeElement.previousSibling.focus()
            } 
            //Odstranění elementu uloženého v proměnné line   
            this.$refs.editorfield.removeChild(line)
            }
        }
        //Uživatel chce odstranit jedno slovo
        else if(what == "word"){
        //Vybrání slova ve kterém je kurzor
        var sel
        if (window.getSelection && (sel = window.getSelection()).modify) {
        sel.collapseToStart();
        sel.modify("move", "backward", "word");
        sel.modify("extend", "forward", "word");
        //Spuštění metody format s příkazem forwardDelete 
        this.format('forwardDelete')
        //Přepnutí focusu na řádek uložený v proměnné line
        this.line.focus()
    }
        }
        //Spuštění metody localStorageSave
        this.localStorageSave()
    },

        //Metoda pro správu stisknutých tlačítek
        keyPress(e){ 
                //Uživatel stiskne šipku nahoru
                if (e.keyCode == 38){
                    /*Nastaví se Timeout na 10 milisekund, po uplynutí tohoto času se spustí metoda arrow s odeslanou
                    hodnotou 38*/
                    setTimeout(this.arrow, 10, 38)
                    //Proměnná line se nastaví na aktivni element
                    this.line = document.activeElement
                }
                //Uživatel stiskne šipku dolů
                else if (e.keyCode == 40){
                    /*Nastaví se Timeout na 10 milisekund, po uplynutí tohoto času se spustí metoda arrow s odeslanou
                    hodnotou 40*/
                    setTimeout(this.arrow, 10, 40)
                    //Proměnná line se nastaví na aktivni element
                    this.line = document.activeElement
                }
                //Uživatel stiskne šipku doleva
                else if (e.keyCode == 37){
                    /*Nastaví se Timeout na 10 milisekund, po uplynutí tohoto času se spustí metoda arrow s odeslanou
                    hodnotou 37*/
                    setTimeout(this.arrow, 10, 37)
                    //Proměnná line se nastaví na aktivni element
                    this.line = document.activeElement
                }
                //Uživatel stiskne šipku doprava
                else if (e.keyCode == 39){
                    /*Nastaví se Timeout na 10 milisekund, po uplynutí tohoto času se spustí metoda arrow s odeslanou
                    hodnotou 39*/
                    setTimeout(this.arrow, 10, 39)
                    //Proměnná line se nastaví na aktivni element
                    this.line = document.activeElement
                }

            //Pokud je uživatel v normal modu
            if (this.insert == true){
                //Uživatel stiskne klávesovou zkratku "CTRL+Z"
                if (e.ctrlKey && e.key === 'z') {
                //Spustí se metoda format s příkazem undo
                this.format('undo')
                }
                //Uživatel stiskne klávesovou zkratku "CTRL+SHIFT+Z"
                else if (e.ctrlKey && e.shiftKey && e.key === 'z') {
                //Spustí se metoda format s příkazem redo
                this.format('redo')
                }
                //Switch pro ostatní tlačítka stisknutá v normal modu
                switch(e.keyCode){
                    //Uživatel stiskne tlačítko i
                    case 73: 
                    //Program zakáže původní funkci tlačítka
                    e.preventDefault()
                    //Proměnná insert se změní na false
                    this.insert = false
                    //Proměnná insert se pošle do componentu BottomPanel
                    this.$emit('insert', this.insert)
                    break;
                    
                    //Uživatel stiskne tlačítko x
                    case 88:
                    //Program zakáže původní funkci tlačítko
                    e.preventDefault()
                    //Spustí se metoda format s příkazem forwardDelete
                    this.format('forwardDelete')
                    //Funkce která přepne focus na předchozí řádek pokud se vymaže ten který je aktivní
                    var previousLine = document.activeElement.previousSibling
                    var x = new MutationObserver(function (e) {
                    if (e[0].removedNodes){
                    previousLine.focus();
                    x.disconnect()
                    }
                    });
                    x.observe(this.$refs.editorfield, { childList: true });
                    break;

                    //Uživatel stiskne tlačítko Tab
                    case 9:
                    //Program zakáže původní funkci tlačítka
                    e.preventDefault()
                    break;
                    
                    //Uživatel stiskne šipku doleva
                    case 37:
                    //Program provede původní funkci tlačítka
                    break;

                    //Uživatel stiskne šipku nahoru
                    case 38:
                    //Program provede původní funkci tlačítka
                    break;

                    //Uživatel stiskne šipku doprava
                    case 39:
                    //Program provede původní funkci tlačítka
                    break;

                    //Uživatel stiskne šipku dolů
                    case 40:
                    //Program provede původní funkci tlačítka
                    break;

                    //Uživatel stiskne tlačítko F5
                    case 116:
                    //Program provede původní funkci tlačítka
                    break;

                    //Zbytek tlačítek v normal modu
                    default: 
                    //Program zakáže původní funkci tlačítka
                    e.preventDefault()
                }
            }
            //Pokud je uživatel v insert modu
            else if (this.insert == false){
                //Při každém stisknutí tlačítka se spustí metoda localStorageSave
                this.localStorageSave()
                //Switch pro stisknutá tlačítka
                switch(e.keyCode){
                    //Uživatel stiskne tlačítko ESC
                    case 27:
                    //Proměnná insert se přepíše na true
                    this.insert = true
                    //Proměnná insert se pošle do componentu BottomPanel
                    this.$emit('insert', this.insert)
                    break;

                    //Uživatel stiskne tlačítko Enter
                    case 13:
                    //Nastaví se Timeout na 10 milisekund, po uplynutí tohoto času se spustí metoda enter
                    setTimeout(this.enter, 10)
                    break;

                    //Uživatel stiskne tlačítko BackSpace
                    case 8:
                    //Funkce která přepne focus na předchozí řádek pokud se vymaže ten který je aktivní
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

    //Metoda pro formátování textu
    format(command, value) {
    //Na element uložený v proměnné line se přepne focus
    this.line.focus()
    //Funkce document.execCommand která čeká na příkaz a hodnotu
    document.execCommand(command, false, value)
    //Spuštění metody localStorageSave
    this.localStorageSave()
 },
    
    //Metoda pro nastavení velikosti písma
    textSize(size){
        //Na element uložený v proměnné line se přepne focus
        this.line.focus()
        //Funkce document.execCommand s předepsaným příkazem čeká která na hodnotu
        document.execCommand("fontSize", false, size)
        //Spuštění metody localStorageSave
        this.localStorageSave()
    },

    //Metoda pro nastavení fontu
    font(fontName){
        //Na element uložený v proměnné line se přepne focus
        this.line.focus()
        //Funkce document.execCommand s předepsaným příkazem čeká která na hodnotu
        document.execCommand("fontName", false, fontName)
        //Spuštění metody localStorageSave
        this.localStorageSave()
    },

    //Metoda pro nastavení barvy písma
    textColor(textcolor){
        //Na element uložený v proměnné line se přepne focus
        this.line.focus()
        //Funkce document.execCommand s předepsaným příkazem čeká která na hodnotu
        document.execCommand("foreColor", false, textcolor)
        //Spuštění metody localStorageSave
        this.localStorageSave()
    },

    //Metoda pro nastavení barvy zvýraznění písma
    backColor(backcolor){
        //Na element uložený v proměnné line se přepne focus
        this.line.focus()
        //Funkce document.execCommand s předepsaným příkazem čeká která na hodnotu
        document.execCommand("backColor", false, backcolor)
        //Spuštění metody localStorageSave
        this.localStorageSave()
    },

    //Metoda pro vytvoření hyperlinku v textu
    link(){
        //Na element uložený v proměnné line se přepne focus
        this.line.focus()
        //Výzva pro zadání url adresu
        let url = prompt("Please enter URL")
        if (url == null || url == ""){
            alert("No url was set")
        }
        else{
        //Spustí se metoda format s přikazem createLink a s poslanou proměnnou url
        this.format('createLink', url)
        }
        //Spuštění metody localStorageSave 
        this.localStorageSave()
    },

    //Metoda pro převedení názvu barvy do hex hodnoty
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
            //Uživatel chce změnit barvu písma
            if (textcolor == true){    
            //spustí se metoda textColor s poslanou proměnnou colorcode
            this.textColor(colorcode)
            }
            //Uživatel chce změnit barvu zvýraznění písma
            else if (textcolor == false){
            //spustí se metoda backColor s poslanou proměnnou colorcode
            this.backColor(colorcode)    
            }
    },

    //Metoda pro ukládání pole editoru a názvu dokumentu do localStorage
    localStorageSave(){
        let field = this.$refs.editorfield.innerHTML
        window.localStorage.setItem('field', JSON.stringify(field))
        window.localStorage.setItem('name', JSON.stringify(this.fileName))
    },

    //Metoda pro načítání pole editoru a názvu dokumentu z localStorage
    localStorageLoad(){
        let field = JSON.parse(window.localStorage.getItem('field'))
        this.$refs.editorfield.innerHTML = field
        this.lines()
        this.fileName = JSON.parse(window.localStorage.getItem('name'))
    },
  
    //Metoda která spravuje zadané příkazy
    commands(){
        //Výzva pro zadání příkazu
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