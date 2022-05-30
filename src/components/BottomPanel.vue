<!--Panel pod oknem editoru, který ukazuje zda-li je uživatel v normal nebo insert modu a název otevřeného dokumentu-->
<template>
    <div class="bottompanel"> 
        <p class="insert" ref="insert"></p><p class="name" ref="name"></p>
    </div>
</template>

<script>
export default {
    name: 'BottomPanel',

    props: { 
        //Proměnná kterou si bere component z nadřazeného componentu a je v ní zda-li je uživatel v normal nebo insert modu
        insert: Boolean,
        //Proměnná kterou si bere component z nadřazeného componentu a je v ní název otevřeného dokumentu 
        fileName: String        
    },

    /*Funkce která se spouští při načtení stránky, vezme si název dokument z proměnné fileName a použije ho v panelu
    a spustí metodu insertShow*/
    mounted:function(){
        this.insertShow()
        this.$refs.name.innerHTML = this.fileName
    },

    /*Funkce která se spouští při změně názvu dokumentu, vezme si název dokument z proměnné fileName a použije ho v 
    panelu a spustí metodu insertShow*/
    updated:function(){
        this.insertShow()
        this.$refs.name.innerHTML = this.fileName
    },

    methods: {

        //Metoda která přepisuje insert/normal ukazatel v panelu pomocí hodnoty v proměnné insert
        insertShow(){
            if (this.insert == true){
                this.$refs.insert.innerHTML = "NORMAL"
            }
            else if (this.insert == false){
                this.$refs.insert.innerHTML = "INSERT"
            }
        }
    }
}
</script>

<style scoped>
    .bottompanel
    {
    border:solid 1px #ddd;
    background:#f4f4f4;
    padding: 5px;
    border-radius:3px;
    width: 1250px;
    margin: auto;
    margin-bottom: 20px;
    color: black;
    display: flex;
    height: 24px;
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    }

    .bottompanel::after{
    content: " ";
    display: block;
    flex: 1;
    }

    .insert{
    margin-left: 5px;
    flex: 1;
    margin-top: auto;
    }
    
    .name{
    flex: 1;
    text-align: center;
    margin-top: auto;
    }
</style>