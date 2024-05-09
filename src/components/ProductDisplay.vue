<template>
    <div>
        <header>
            <slot name="header">
            </slot>
        </header>
        <slot name="bag"></slot>
        <main>
            <slot>
                <!-- ketika props isLoading bernilai true, menampilkan skeleton untuk memberikan visual loading -->
                <div v-if="isLoading" class="container">
                    <div class="product-wrapper">
                        <div class="skeleton image"></div>
                        <div class="product-info">
                            <div class="skeleton title"></div>
                            <div class="product-card">
                                <div class="head">
                                    <div class="skeleton card tag"></div>
                                    <div class="skeleton card tag"></div>
                                </div>
                                <div class="skeleton card description1"></div>
                                <div class="skeleton card description1"></div>
                                <div class="skeleton card description2"></div>
                                <div class="skeleton card price"></div>
                            </div>
                        </div>
                    </div>
                </div>
                <!-- ketika props isLoading bernilai false, menampilkan produk -->
                <div v-else class="container">
                    <!-- ketika kategori produk merupakan men's clothing atau women's clothing akan menampilkan background -->
                    <slot v-if="
                        product.category === 'men\'s clothing' || 
                        product.category === 'women\'s clothing'" 
                        name="product-background"
                    ></slot>
                    <div class="product-wrapper">
                        <div class="product-image-wrapper">
                            <!-- ketika kategori produk merupakan men's clothing atau women's clothing akan menampilkan gambar -->
                            <img 
                                v-if="
                                    product.category === 'men\'s clothing' || 
                                    product.category === 'women\'s clothing'" 
                                :src="product.image" 
                                :alt="product.title" 
                                class="product-image"
                            >
                            <!-- ketika kategori produk bukan men's clothing atau women's clothing tidak akan menampilkan gambar -->
                            <div v-else class="product-unavailable">
                                <slot name="unavailable-icon"></slot>
                                <h2 class="unavailable-text">No product's image</h2>
                            </div>
                        </div>
                        <div class="product-info">
                            <!-- ketika kategori produk merupakan men's clothing akan menampilkan judul -->
                            <h1 
                                v-if="product.category === 'men\'s clothing' || product.category === 'women\'s clothing'" 
                                class="product-title"
                                :class="product.category === 'men\'s clothing' ? 'men' : 'women'"
                            >
                                {{ product.title }}
                            </h1>
                            <!-- ketika kategori produk bukan men's clothing atau women's clothing akan menampilkan pesan produk tidak tersedia -->
                            <div v-else class="product-unavailable-box">
                                <div class="box">
                                <h1 class="product-title">Oops, sorry this product is currently unavailable</h1>
                                <!-- tombol untuk menampilkan produk selanjutnya dengan emit 'nextProduct' dengan event nextProduct() pada parent  -->
                                <button 
                                    class="btn-next"
                                    @click="handleNextProduct">
                                    Next product
                                    <slot name="next-icon"></slot>
                                </button>
                                </div>
                            </div>
                            <!-- ketika kategori produk merupakan men's clothing atau women's clothing akan menampilkan card produk -->
                            <div
                                v-if="
                                    product.category === 'men\'s clothing' || 
                                    product.category === 'women\'s clothing'" 
                                class="product-card"
                            >
                                <div class="head">
                                    <span 
                                        v-if="product.category === 'men\'s clothing' || product.category === 'women\'s clothing'"
                                        class="product-category"
                                        :class="product.category === 'men\'s clothing' ? 'men' : 'women'"
                                    >
                                        {{ product.category }}
                                    </span>
                                    <div class="rate-box">
                                        <span>{{ product.rating.rate }}</span>
                                        <slot name="rating-icon"></slot>
                                    </div>
                                </div>
                                <p class="product-description">{{ product.description }}</p>
                                <span class="product-price">${{ product.price }}</span>
                                <slot name="qty"></slot>
                                <!-- tombol untuk menampilkan produk selanjutnya dengan emit 'addToBag' dengan event addToBag() pada parent  -->
                                <div class="product-actions">
                                    <button 
                                        class="btn-add"
                                        :class="product.category === 'men\'s clothing' ? 'men' : 'women'"
                                        @click="handleAddToBag"
                                    >
                                        Add to bag
                                    </button>
                                    <!-- tombol untuk menampilkan produk selanjutnya dengan emit 'nextProduct' dengan event nextProduct() pada parent  -->
                                    <button 
                                        class="btn-next"
                                        :class="product.category === 'men\'s clothing' ? 'men' : 'women'" 
                                        @click="handleNextProduct">
                                        Next product
                                        <slot name="next-icon"></slot>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </slot>
        </main>
    </div>
</template>

<script>
    export default {
        name: "ProductDisplay",
        props: {
            product: Object,
            isLoading: Boolean
        },
        methods: {
        
            // Emits event nextProduct() pada parent
            handleNextProduct() {
                this.$emit('nextProduct', this.product.id)
            },
            // Emits event addToBag() pada parent
            handleAddToBag() {
                this.$emit('addToBag', this.product)
            }
        }
    }
</script>

<style scoped src="@/assets/css/style.css">
</style>
