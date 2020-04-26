<template>
    <tr :class="{active : activeRow}">
        <td>
            <label class="checkbox--green">
                <input type="checkbox" :value=product.id v-model="selected">
                <span class="checkmark"></span>
            </label>
        </td>
        <td v-for="column in showColumns" :key="column.id">{{product[column.name]}}</td>
        <td>
            <div class="table__delete">
                <Dropdown :clear="true" :align="'center'" ref="dropdownTable">
                    <template v-slot:header>
                        <button @click="activeRow = true" class="table-item__delete">
                            <img src="../assets/img/trash.svg" alt="">
                            delete
                        </button>
                    </template>

                    <template v-slot:body>
                        <div class="table-delete">
                            <div class="table-delete__header">
                                <p>Are you sure you want to <b>delete item</b>?</p>
                                <p v-if="errorDelete">Server error! Try again ?</p>
                            </div>
                            <div class="table-delete__body">
                                <button @click="closeDropdown" class="button--border">Cancel</button>
                                <button @click="deleteProduct([product.id])" class="button--green">
                                    Confirm
                                </button>
                            </div>
                        </div>
                    </template>
                </Dropdown>
            </div>
        </td>
    </tr>
</template>

<script>
    import Dropdown from "./Dropdown";

    export default {
        name: "TableItem",
        components: {Dropdown},
        props: {
            product: Object,
            showColumns: Array,
            value: Array,
            deleteItem: Function,
        },
        data() {
            return {
                errorDelete: false,
                activeRow: false,
            }
        },
        created: function () {
            let self = this;

            window.addEventListener('click', function (e) {
                if (!self.$el.contains(e.target)) {
                    self.activeRow = false
                }
            })
        },
        computed: {
            selected: {
                get() {
                    return this.value;
                },
                set(value) {
                    this.$emit('input', value);
                },
            },
        },
        methods: {
            deleteProduct(arr) {
                this.errorDelete = false
                this.deleteItem(arr)
                    .catch(() => {
                        this.errorDelete = true
                    })
            },
            closeDropdown() {
                this.$refs.dropdownTable.hide()
                this.activeRow = false
            }
        }
    }
</script>

<style lang="scss">
    .table-item__delete {
        display: flex;
        align-items: center;
        background: transparent;
        color: #5B5E77;
        font-size: 14px;

        img {
            margin-right: 5px;
        }
    }
</style>