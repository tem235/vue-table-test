<template>
    <div id="app">
        <TableContainer/>
    </div>
</template>

<script>
    import TableContainer from "./components/TableContainer";

    export default {
        name: 'App',
        components: {
            TableContainer,
        }
    }
</script>

<style>
    #app {
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
