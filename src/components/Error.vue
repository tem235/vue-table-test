<template>
    <div class="error">
        <h3>Oooops, something went wrong.</h3>
        <button @click="$emit('reload')" class="button--green">Reload</button>
    </div>
</template>

<script>
    export default {
        name: "Error"
    }
</script>

<style lang="scss">
    .error {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        h3 {
            margin-bottom: 30px;
        }
    }
</style>