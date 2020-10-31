<template>
<div>
    <!-- Women Banner Section Begin -->
    <section class="women-banner spad">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-12 mt-5" v-if="products.length > 0">
                    <carousel :nav="false" :items="3" :autoplay="true">

                        <div class="product-item" v-for="itemProduct in products" :key="itemProduct.id">
                            <div class="pi-pic">
                                <img style="max-height:350px" :src="itemProduct.galleries[0].photo" alt="" />
                                <ul>
                                    <li class="w-icon active" @click="saveKeranjang(itemProduct.id, itemProduct.name, itemProduct.price, itemProduct.galleries[0].photo)">
                                        <a href="#"><i class="icon_bag_alt"></i></a>
                                    </li>
                                    <li class="quick-view">
                                        <router-link :to="'/product/'+itemProduct.id">+ Quick View</router-link>
                                    </li>
                                </ul>
                            </div>
                            <div class="pi-text">
                                <div class="catagory-name">{{itemProduct.type}}</div>
                                <h5>{{itemProduct.name}}</h5>

                                <div class="product-price">
                                    {{formatPrice(itemProduct.price)}}
                                    <span>{{formatPrice(itemProduct.price*1.5)}}</span>
                                </div>
                            </div>
                        </div>
                    </carousel>
                </div>
                <div class="col-lg-12" v-else>
                    <p>
                        Product Tidak Tersedia
                    </p>
                </div>
            </div>
        </div>
    </section>
    <!-- Women Banner Section End -->
</div>
</template>

<script>
import carousel from 'vue-owl-carousel'
import axios from 'axios'
export default {
    name: 'WomenBannerShayna',
    data() {
        return {
            products: [],
            keranjangUser: []
        }
    },
    components: {
        carousel
    },
    methods: {
        saveKeranjang(idProduct, nameProduct, priceProduct, photoProduct) {
            let saveProduct = {
                id: idProduct,
                name: nameProduct,
                price: priceProduct,
                photo: photoProduct,
                quantity: 1
            }
            // let cart = localStorage.getItem('keranjangUser')
            this.keranjangUser.push(saveProduct)
            const parsed = JSON.stringify(this.keranjangUser)
            localStorage.setItem('keranjangUser', parsed)

            // let item = JSON.parse(localStorage.getItem('keranjangUser'))
            // let oldItem = item.find(el => el.id === idProduct)
            // if (oldItem !== undefined) {
            //     oldItem.quantity += 1
            //     let itemIndex = item.findIndex(el => el.id === oldItem.id)
            //     item[itemIndex] = oldItem
            //     localStorage.setItem('keranjangUser', JSON.stringify(item))
            //     this.keranjangUser.push(saveProduct)
            // }

            // console.log(item.find(el => el.id === idProduct))
            // window.location.reload()
        },

        formatPrice(value) {
            // let val = (value/1).toFixed(2).replace('.', ',')
            let val = (value / 1)
            return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
        },
    },

    mounted() {
        if (localStorage.getItem('keranjangUser')) {
            try {
                this.keranjangUser = JSON.parse(localStorage.getItem('keranjangUser'));
            } catch (e) {
                localStorage.removeItem('keranjangUser');
            }
        }
        axios
            .get("http://127.0.0.1:8002/api/products")
            .then(res => (this.products = res.data.data.data))
            .catch(err => console.log(err))
    }
}
</script>

<style scoped>
.product-item {
    margin-right: 25px;
}
</style>
