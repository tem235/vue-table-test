<template>
    <div class="container">
        <div class="table">
            <h1>Table UI</h1>
            <div class="table-header">
                <div class="table-header__sorting">
                    <p><b>Sorting by:</b></p>
                    <label v-for="column in columns" :key="column.id"
                           :class="{activeGroup : selectedGroup === column.name}">
                        <input type="radio" :disabled="!column.value" :value="column.name" name="group"
                               v-model="selectedGroup">{{column.text}}</label>
                </div>

                <div class="table-header-actions">
                    <Dropdown :clear="true" ref="dropdownHeader">
                        <template v-slot:header>
                            <button :disabled="selectedProducts.length === 0"
                                    class="button--green table-header-actions__delete">
                                Delete
                            </button>
                        </template>

                        <template v-slot:body>
                            <div class="table-delete">
                                <div class="table-delete__header">
                                    <p>Are you sure you want to <b>delete items</b>?</p>
                                    <p v-if="errorDelete" class="table-delete__header">Server error! Try again ?</p>
                                </div>
                                <div class="table-delete__body">
                                    <button @click="$refs.dropdownHeader.hide" class="button--border">Cancel</button>
                                    <button @click="deleteTableItems(selectedProducts)" class="button--green">
                                        Confirm
                                    </button>
                                </div>
                            </div>
                        </template>
                    </Dropdown>

                    <Dropdown class="table-header-actions__pages">
                        <template v-slot:header>
                            <p>{{itemsPerPage}} Per Page</p>
                        </template>

                        <template v-slot:body>
                            <div class="dropdown-items">
                                <label>
                                    <input v-model.number="itemsPerPage" value="10" type="radio" name="pageSize">
                                    10 Per Page
                                </label>
                                <label>
                                    <input v-model.number="itemsPerPage" value="15" type="radio" name="pageSize">
                                    15 Per Page
                                </label>
                                <label>
                                    <input v-model.number="itemsPerPage" value="20" type="radio" name="pageSize">
                                    20 Per Page
                                </label>
                            </div>
                        </template>
                    </Dropdown>

                    <div class="table-header-actions__pagination">
                        <button @click="prevPage" :disabled="currentPage === 0">
                            <img src="../assets/img/chevron.svg" alt="">
                        </button>
                        <p><b>{{currentPage + 1}}</b> of <b v-if="pageCount">{{pageCount}}</b> <b v-else>1</b></p>
                        <button @click="nextPage" :disabled="currentPage === pageCount - 1">
                            <img src="../assets/img/chevron.svg" alt="">
                        </button>
                    </div>

                    <Dropdown>
                        <template v-slot:header>
                            <p>{{columnsCount}} columns selected</p>
                        </template>

                        <template v-slot:body>
                            <div class="dropdown-items">
                                <label class="checkbox--green">
                                    <input type="checkbox" v-model="selectedAllColumns" @change="selectAllColumns">
                                    <span class="checkmark"></span>
                                    <span class="checkbox__text"><b>Select All</b></span>
                                </label>
                                <label v-for="column in columns" :key="column.id" class="checkbox--green">
                                    <input type="checkbox" :checked="column.value" @change="selectColumn(column.id)">
                                    <span class="checkmark"></span>
                                    <span class="checkbox__text">{{column.text}}</span>
                                </label>
                            </div>
                        </template>
                    </Dropdown>

                </div>
            </div>

            <table>
                <thead>
                <th></th>
                <th v-for="(column, index) in showColumns" :key="column.id">
                    <div class="">
                        <p>{{column.text}}</p>
                        <button v-if="index === 0 && sort === 'asc'" @click="sorting(column.name)" class="button--asc">
                            <img src="../assets/img/arrow.svg" alt="">
                        </button>
                        <button v-if="index === 0 && sort === 'desc'" @click="sorting(column.name)"
                                class="button--desc">
                            <img src="../assets/img/arrow.svg" alt="">
                        </button>
                    </div>
                </th>
                <th></th>
                </thead>
                <tbody>
                <TableItem
                        v-for="product in paginatedData"
                        :key="product.id"
                        v-model="selectedProducts"
                        :product="product"
                        :showColumns="showColumns"
                        :deleteItem="deleteItems"
                />
                </tbody>
            </table>

            <Loader v-if="loading"/>
            <Error v-if="error" @reload="fetchData"/>
        </div>


    </div>
</template>

<script>
    import Loader from "./Loader";
    import Error from "./Error";
    import Dropdown from "./Dropdown";
    import TableItem from "./TableItem";

    export default {
        name: "Table",
        components: {TableItem, Dropdown, Error, Loader},
        props: {
            list: Array,
            columns: Array,
            fetchData: Function,
            deleteItems: Function,
            loading: Boolean,
            error: Boolean,
        },
        data() {
            return {
                selectedGroup: null,
                selectedAllColumns: true,
                itemsPerPage: 10,
                currentPage: 0,
                selectedProducts: [],
                sort: 'asc',
                errorDelete: false,
            }
        },
        mounted() {
            this.selectedGroup = this.columns[0].name
        },
        watch: {
            selectedGroup: function (val) {
                let foundColumn = this.columns.find(column => column.name === val)
                this.columns.map((column, index) => {
                    this.columns[index] !== foundColumn ? this.$set(this.columns[index], 'priority', index + 1) : this.$set(this.columns[index], 'priority', 0)
                })
                this.sort = 'asc'
            },
            itemsPerPage: function () {
                this.currentPage = 0
            }
        },
        computed: {
            showColumns() {
                return this.columns.filter(column => column.value === true).sort((a, b) => {
                    return a.priority > b.priority ? 1 : -1
                })
            },
            columnsCount() {
                return this.columns.filter(column => column.value === true).length
            },
            pageCount() {
                return Math.ceil(this.list.length / this.itemsPerPage)
            },
            paginatedData() {
                let start = this.currentPage * this.itemsPerPage
                let end = start + this.itemsPerPage

                if (this.list.length > 0) {
                    return this.list.slice(start, end)
                }
                return null
            }
        },
        methods: {
            selectColumn(id) {
                this.columns.find(column => {
                    if (column.id === id) {
                        column.value ? this.$set(column, 'value', false) : this.$set(column, 'value', true)
                    }
                })
                this.columnsCount !== this.columns.length ? this.selectedAllColumns = false : this.selectedAllColumns = true

            },
            selectAllColumns() {
                if (this.selectedAllColumns === true) {
                    this.columns.map(column => {
                        this.$set(column, 'value', true)
                    })
                    this.selectedAllColumns = true
                }
            },
            nextPage() {
                this.currentPage++
            },
            prevPage() {
                this.currentPage--
            },
            sorting(key) {
                if (this.sort === 'asc') {
                    this.list.sort((a, b) => {
                        return a[key] > b[key] ? 1 : -1
                    })
                    this.sort = 'desc'
                    this.currentPage = 0
                } else {
                    this.list.reverse()
                    this.sort = 'asc'
                    this.currentPage = 0
                }
            },

            deleteTableItems(arr) {
                this.deleteItems(arr)
                    .then(() => {
                        this.selectedProducts = []
                        this.$refs.dropdownHeader.hide()
                        this.errorDelete = false
                    })
                    .catch(() => {
                        this.errorDelete = true
                    })
            }
        }
    }
</script>

<style lang="scss">
    .table {
        display: flex;
        flex-direction: column;
        align-items: center;

        h1 {
            align-self: flex-start;
        }
    }

    .table-header {
        display: flex;
        align-items: center;
        justify-content: space-between;
        height: 64px;
        width: 100%;
        border-top: 1px solid #EDEDED;
    }

    .table-header__sorting {
        display: flex;
        align-items: center;

        b {
            font-size: 14px;
            margin-right: 8px;
        }

        label {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-right: 8px;
            height: 32px;
            border-radius: 2px;
            padding: 3px 8px;
            font-size: 14px;


            &:hover, &.activeGroup {
                background: #00A11E;
                color: #fff;
                cursor: pointer;
            }

            input {
                display: none;
            }
        }
    }

    .table-header-actions {
        display: flex;
        align-items: center;

        .table-header-actions__delete {
            margin-right: 12px;
        }

        .table-header-actions__pages {
            margin-right: 12px;
        }

        .dropdown-items {
            display: flex;
            flex-direction: column;

            label {
                margin-bottom: 12px;

                &:last-child {
                    margin-bottom: 0;
                }

                &:hover {
                    cursor: pointer;
                }

                input[type=radio] {
                    display: none;
                }
            }
        }


        .table-header-actions__pagination {
            display: flex;
            align-items: center;
            margin-right: 12px;
            font-size: 14px;

            button {
                width: 32px;
                height: 32px;
                background: #fff;
                border: 1px solid #C6CBD4;
                box-sizing: border-box;
                border-radius: 2px;

                &:hover {
                    cursor: pointer;
                }

                &:first-child {
                    margin-right: 6px;

                    img {
                        transform: rotate(90deg);
                    }
                }

                &:last-child {
                    margin-left: 6px;

                    img {
                        transform: rotate(-90deg);
                    }
                }
            }
        }
    }

    .table-delete {
        width: 250px;
        display: flex;
        flex-direction: column;

        .table-delete__header {

            p {
                font-size: 14px;
                margin-top: 0;
            }
        }

        .table-delete__body {
            display: flex;
            justify-content: center;
            align-items: center;

            button {
                margin: 0 5px;
            }
        }

    }

    table {
        width: 100%;
        border-collapse: collapse;
        margin-bottom: 50px;

        thead {
            background: #fff;
            border-bottom: 1px solid #EDEDED;

            th {
                height: 56px;
                text-align: left;

                &:first-child {
                    padding-left: 35px;
                }

                &:nth-child(2) {
                    color: #00A11E;

                    .button--asc {
                        background: transparent;
                    }

                    .button--desc {
                        background: transparent;

                        img {
                            transform: rotate(180deg);
                        }


                    }
                }

                div {
                    width: 100%;
                    display: flex;
                    align-items: center;

                    p {
                        margin: 0 5px 0 0;
                    }
                }
            }
        }

        tbody {

            tr {
                height: 50px;
                background: #fff;
                transition: 0.3s;

                &:nth-child(2n) {
                    background: #F8F9FA;
                }

                &:hover, &.active {
                    background: #EDF8EF;
                    cursor: pointer;

                    .table__delete {

                        button {
                            display: flex;
                        }
                    }
                }

                td {
                    text-align: left;

                    &:first-child {
                        padding-left: 35px;
                    }


                }

                .table__delete {
                    display: flex;
                    justify-content: flex-end;
                    width: 80px;

                    button {
                        display: none;
                    }
                }
            }
        }
    }

</style>