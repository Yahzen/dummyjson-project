<template>
    <div class="productContainer">
        <div v-for="product in visibleProducts" :key="product.id" class="productObject">
            <img :src="product.images[0]" alt="ProductImage" class="productImage">
            <h2> {{ product.title }} </h2>
        </div>
        <div class="pagInterface">
            <button @click="firstPage" style="border-top-left-radius:8px; border-bottom-left-radius: 8px;">
                << </button>
                    <button @click="pageDown">
                        < </button>
                            <div v-for="num in pageNumber" :key="num">
                                <button @click="setCurrentPage(num)"> {{ num + 1 }} </button>
                            </div>
                            <button @click="pageUp"> > </button>
                            <button @click="lastPage" style="border-top-right-radius:8px; border-bottom-right-radius: 8px;"> >> </button>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

//Variables
const productData = ref([])
const arrLength = computed(() => productData.value.length)
const currentPage = ref(0)
const pageSize = 8
//Computed
const visibleProducts = computed(() => updateVisibleProducts(productData, pageSize, currentPage.value))
const upperLimit = computed(() => Math.ceil(arrLength.value / pageSize))
const pageNumber = computed(() => pageShow())

//Methods
//Variable Fetch from dummyjson.com to limit scope if needed. Default = Entire list
function fetchData(limit = 0, skip = 0) {
    fetch(`https://dummyjson.com/products?limit=${limit}&skip=${skip}&select=title,images`)
        .then(res => res.json())
        .then(data => {
            productData.value = data.products
        })
        .catch(error => console.error('Error fetching data:', error))
}

//Pagination-Methods
function updateVisibleProducts(data, pageSize, currentPage) {
    if (currentPage == 0) {
        return data.value.slice(0, pageSize)
    }
    else {
        return data.value.slice(currentPage * pageSize, currentPage * pageSize + pageSize)
    }
}

function pageUp() {
    if (currentPage.value < upperLimit.value) {
        currentPage.value++
    }
}

function pageDown() {
    if (currentPage.value > 0) {
        currentPage.value--
    }
}

function setCurrentPage(i) {
    currentPage.value = i
}

function firstPage() {
    currentPage.value = 0
}

function lastPage() {
    currentPage.value = upperLimit.value - 1
}

function pageShow() {
    let temp = currentPage.value
    if (temp <= 3) {
        return [0, 1, 2, 3, 4]
    } else if (temp >= 4 && temp < upperLimit.value - 3) {
        return [temp - 2, temp - 1, temp, temp + 1, temp + 2]
    } else {
        return [upperLimit.value - 5, upperLimit.value - 4, upperLimit.value - 3, upperLimit.value - 2, upperLimit.value - 1]
    }
}


//Lifecycle
onMounted(() => {
    fetchData()
})


</script>

<style scoped>
.productContainer {
    align-items: center;
    padding: 20px;
    display: flex;
    flex-direction: column;
}

.productObject{
    display: flex;
    align-items:center;
    background-color: #303841;
    margin: 5px;
    padding: 5px;
    border-radius: 8px;
    width: 400px;
    height: auto;
}

.productObject:hover{
    background-color: #272727;
}

.productImage {
    width: 100px;
    height: auto;
    margin-right: 15px;
}

h2 {
    font-size: 17px;
    color: #fff;
    align-items: center;
    flex-grow: 1;
}

.text {
    font-size: 16px;
}

.pagInterface {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 10px;
    border-radius: 15px;
}

.pagInterface button{
    padding: 10px;
    background-color: #1d1f23;
    color: #fff;
    cursor: pointer;
    transition: background-color 0.3s;
    border: none;
}

.pagInterface button:hover {
    background-color:#555
}

.active{
    background-color: #555
}
</style>