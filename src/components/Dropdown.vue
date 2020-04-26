<template>
    <div class="dropdown" :class="{'dropdown--clear' : clear, 'dropdown--align-center' : align}">
        <div @click="show = !show" class="dropdown__header">
            <slot name="header"></slot>
            <img class="dropdown__img" src="../assets/img/chevron.svg" alt="">
        </div>

        <div v-if="show" class="dropdown__body">
            <slot name="body"></slot>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Dropdown",
        props: {
            clear: Boolean,
            align: String,
        },
        data() {
            return {
                show: false
            }
        },
        created: function () {
            let self = this;

            window.addEventListener('click', function (e) {
                if (!self.$el.contains(e.target)) {
                    self.show = false
                }
            })
        }
        ,
        methods: {
            hide() {
                this.show = false
            }
        },
    }
</script>

<style lang="scss">
    .dropdown {
        position: relative;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 32px;
        padding: 0 8px;
        background: #fff;
        border: 1px solid #D5DAE0;
        box-sizing: border-box;
        border-radius: 2px;

        .dropdown__header {
            width: max-content;
            display: flex;

            img {
                margin-left: 5px;
            }

            &:hover {
                cursor: pointer;
            }

            p {
                margin: 0;
                font-size: 14px;
                color: #5B5E77;
            }
        }

        .dropdown__body {
            position: absolute;
            display: flex;
            flex-direction: column;
            width: max-content;
            background: #FFFFFF;
            box-shadow: 0px 2px 8px rgba(0, 0, 0, 0.16);
            border-radius: 4px;
            padding: 15px;
            top: 48px;
            left: 0;
            z-index: 2;
        }

        &--clear {
            background: transparent;
            border: none;

            .dropdown__img {
                display: none;
            }
        }

        &--align-center {
            .dropdown__body {
                left: -50%;
            }
        }
    }


</style>