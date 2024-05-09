<template>
  <ProductDisplay :isLoading="isLoading" :product="product" @nextProduct="nextProduct" @addToBag="addToBag">
    <template #header>
        <nav class="navbar container">
          <h2>FASHION STORE</h2>
          <!-- tombol untuk membuka dan menutup keranjang sekaligus menampilkan jumlah item di keranjang -->
          <button class="btn-notif" @click="toggleBag">
            <ShoppingBagIcon />
            <span v-if="bag.length > 0">{{ bag.length }}</span>
          </button>
        </nav>
    </template>
    <template #bag>
      <div class="sidebar right" :class="{ active: bagOpened }">
          <div class="wrapper">
            <button class="close-sidebar" @click="toggleBag">
              <XIcon size="36" />
            </button>
            <div class="title">My List</div>
            <div class="content-wrapper">
              <!-- ketika terdapat item di keranjang akan menampilkan daftar item yang telah ditambahkan ke keranjang -->
              <ul v-if="bag.length > 0" class="bag-list">
                <li v-for="product in bag" :key="product.id">
                  <div class="bag-item">
                    <img :src="product.item.image" :alt="product.id">
                    <div>
                      <h4>{{ product.item.title }}</h4>
                      <hr>
                      <span>${{ product.item.price }}</span>
                      <span>
                        x
                      </span>
                      <span>{{ product.qty }}</span>
                    </div>
                  </div>
                  <!-- tombol untuk menghapus item dari keranjang -->
                  <button @click="removeFromBag(product)" class="btn-item-remove">
                    <Trash2Icon size="24" />
                  </button>
                </li>
              </ul>
              <!-- ketika tidak terdapat item pada keranjang akan menampilkan pesan mengenai keranjang -->
              <div v-else class="empty-bag">
                <ShoppingBagIcon size="36" />
                <span>Your bag is empty</span>
              </div>
            </div>
            <!-- ketika terdapat item di keranjang akan menampilkan total belanja dan tombol checkout -->
            <div v-if="bag.length > 0" class="action">
              <div class="price-box">
                <span>Total</span>
                <h2 class="total-price">${{ totalPrice.toFixed(2) }}</h2>
              </div>
              <button class="btn-checkout" @click="toggleBag">Checkout my shoplist</button>
            </div>
          </div>
      </div>
    </template>
    <template #default>
    </template>
    <template #rating-icon>
      <!-- menampilkan rate produk -->
      <div v-for="index in parseInt(productRate)" :key="index">
        <StarIcon v-if="product.category === 'men\'s clothing'" class="product-rate men active" />
        <StarIcon v-else-if="product.category === 'women\'s clothing'" class="product-rate women active" />
        <StarIcon v-else class="product-rate active" />
      </div>
      <div v-for="index in ( 5 - parseInt(productRate) )" :key="-index">
        <StarIcon class="product-rate" />
      </div>
    </template>
    <template #product-background>
      <div class="background-wrapper">
        <h1 class="product-brand" :class="[product.category === 'men\'s clothing' ? 'men' : 'women', !isLoading ? 'in' : '']">
          {{ productBrand }}
        </h1>
      </div>
    </template>
    <template #next-icon>
      <ChevronRightIcon size="36" />
    </template>
    <template #unavailable-icon>
      <ImageIcon size="128" class="unavailable-text" />
    </template>
    <template #qty>
      <!-- menampilkan input untuk menambahkan dan mengurangi jumlah produk -->
      <div class="qty-box">
        <span>Quantity:</span>
        <button class="qty-increase" @click="productQty++">
          <PlusIcon size="24" />
        </button>
        <input type="number" v-model="productQty">
        <button class="qty-decrease" @click="productQty--">
          <MinusIcon size="24" />
        </button>
      </div>
    </template>
  </ProductDisplay>
</template>

<script>
import ProductDisplay from "./components/ProductDisplay.vue";
import { 
          ShoppingBagIcon, 
          StarIcon, 
          ChevronRightIcon, 
          ImageIcon, 
          XIcon, 
          PlusIcon, 
          MinusIcon,
          Trash2Icon
       } from "vue-feather-icons";

export default {
  name: 'App',
  data: () => ({
    isLoading: true,
    productId: 1,
    product: null,
    productBrand: '',
    productRate: 0,
    productQty: 1,
    bag: [],
    bagOpened: false,
    totalPrice: 0,
  }),
  components: {
    ProductDisplay,
    ShoppingBagIcon,
    StarIcon,
    ChevronRightIcon,
    ImageIcon,
    XIcon,
    PlusIcon,
    MinusIcon,
    Trash2Icon
  },
  methods: {
    // function untuk mengambil data produk dari API dan menyimpannya dalam variabel product.
    async getProducts() {
      const res = await fetch(`https://fakestoreapi.com/products/${this.productId}`)
      let data = await res.json()
      this.product = data
      
      let firstWord = data.title.split(' ')[0]
      this.productBrand = firstWord

      this.productRate = data.rating.rate.toFixed()

      this.isLoading = false
    },
    // function yang digunakan untuk mengupdate productId yang nantinya digunakan untuk mengambil data produk pada function getProducts().
    nextProduct(productId) {
      this.productId = productId < 20 ? productId + 1 : 1
      this.isLoading = true
    },
    // function untuk menambahkan produk ke keranjang sekaligus menambahkan total belanja
    addToBag () {
      this.bag.push({
        id: this.bag.length + 1,
        item: this.product,
        qty: this.productQty
      })
      this.totalPrice = this.totalPrice + (this.product.price * this.productQty)
    },
    // function untuk membuka/menutup sidebar keranjang
    toggleBag() {
      this.bagOpened = this.bagOpened ? false : true
    },
    // function untuk menghapus produk dari keranjang sekaligus mengurangi total belanja
    removeFromBag(product) {
      this.bag = this.bag.filter(item => item.id !== product.id)
      this.totalPrice = this.totalPrice - (product.item.price * product.qty)
    }
  },
  // memanggil function getProducts saat komponen 'mounted'
  mounted() {
    this.getProducts()
  },
  watch: {
    // memanggil function getProducts() saat nilai pada state 'productId' berubah
    productId() {
      this.getProducts()
    }
  }
}
</script>

<style>
body {
  margin: 0 !important;
  padding: 0 !important;
}
</style>
