<template>
    <div class="bottompanel"> 
        <p class="insert" ref="insert"></p><p class="name" ref="name"></p>
    </div>
</template>

<script>
export default {
    name: 'BottomPanel',

    props: { insert: Boolean,
             fileName: String        
    },

    mounted:function(){
        this.insertShow()
        this.$refs.name.innerHTML = this.fileName
    },

    updated:function(){
        this.insertShow()
        this.$refs.name.innerHTML = this.fileName
    },

    methods: {
        insertShow:function(){
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