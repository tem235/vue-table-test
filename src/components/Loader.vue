<template>
    <div class="loader">
        <div class="spinner">

        </div>
    </div>
</template>

<script>
    export default {
        name: "Loader"
    }
</script>

<style lang="scss">
    .loader {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 100%;
        height: 200px;
    }

    .spinner {
        transform: translate(-50%, -50%);
        width: 60px;
        height: 60px;
        border-radius: 50%;
        border: 4px solid #00A11E;
        border-right-color: transparent;
        transform-origin: center;
        animation: rotate 1s linear infinite;
    }

    @keyframes rotate {
        from {
            transform: rotate(0);
        }
        to {
            transform: rotate(360deg);
        }
    }
</style>