<template>
    <Table
            :list="productList"
            :columns="columns"
            :fetchData="fetchData"
            :deleteItems="deleteData"
            :loading="loading"
            :error="error"
    />
</template>

<script>
    import {getProducts, deleteProducts} from '../api/request'
    import Table from "./Table";

    export default {
        name: "TableContainer",
        components: {Table},
        data() {
            return {
                productList: [],
                columns: [
                    {id: 0, name: 'product', text: 'Product(100g serving)', value: true, priority: 0},
                    {id: 1, name: 'calories', text: 'Calories', value: true, priority: 1},
                    {id: 2, name: 'fat', text: 'Fat (g)', value: true, priority: 2},
                    {id: 3, name: 'carbs', text: 'Carbs (g)', value: true, priority: 3},
                    {id: 4, name: 'protein', text: 'Protein (g)', value: true, priority: 4},
                    {id: 5, name: 'iron', text: 'Iron (%)', value: true, priority: 5},
                ],
                selectedItems: [],
                loading: false,
                error: false,
            }
        },
        mounted() {
            this.fetchData()
        },
        methods: {
            fetchData() {
                this.loading = true
                this.error = false
                getProducts()
                    .then(res => {
                        this.productList = res
                        this.loading = false
                    })
                    .catch(err => {
                        console.log(err)
                        this.loading = false
                        this.error = true
                    })
            },
            deleteData(arr) {
                return deleteProducts()
                    .then(() => {
                        arr.map((id => {
                            let indexProduct = this.productList.findIndex(product => {
                                return product.id === id
                            })
                            this.productList.splice(indexProduct, 1)
                        }))
                    })
            },
        }
    }
</script>

<style scoped>

</style>