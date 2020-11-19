<template>
    <div id="app">
        <div id="nav">
<!--            <router-link to="/">Home</router-link>-->
            Simon Game
        </div>
        <router-view/>
    </div>
</template>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        display: block;
        color: #2c3e50;
    }

    #nav {
        padding: 30px;
        color: black;
        font-size: 40px;
    }

    #nav a {
        font-weight: bold;
        color: #2c3e50;
    }

    #nav a.router-link-exact-active {
        color: #42b983;
    }
</style>
