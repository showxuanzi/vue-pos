<template>
    <div id="app">
        <LeftNav></LeftNav>
        <div class="main">
            <router-view/>
        </div>
    </div>
</template>

<script>
    import LeftNav from '@/components/common/LeftNav'
    export default {
      name: 'App',
      components:{
        LeftNav
      }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
    }
    .main{
        float: left;
        height: 100%;
        width: 95%;
        overflow: auto;
        background-color: #eff2f7;
    }
</style>
