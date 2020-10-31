<template>
<div>
    <HeaderShayna />
    <div class="text-left">
        <!-- Breadcrumb Section Begin -->
        <div class="breacrumb-section">
            <div class="container">
                <div class="row">
                    <div class="col-lg-12">
                        <div class="breadcrumb-text product-more">
                            <a href="/"><i class="fa fa-home"></i> Home</a>
                            <span>Shopping Cart</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Breadcrumb Section Begin -->
        <!-- Shopping Cart Section Begin -->
        <section class="shopping-cart spad">
            <div class="container">
                <div class="row">
                    <div class="col-lg-8">
                        <div class="row">
                            <div class="col-lg-12">
                                <div class="cart-table">
                                    <table>
                                        <thead>
                                            <tr>
                                                <th>Image</th>
                                                <th class="p-name text-center">Product Name</th>
                                                <th>Price</th>
                                                <th>Action</th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <tr v-for="(keranjang, index) in keranjangUser" :key="index">
                                                <td class="cart-pic first-row">
                                                    <img :src="keranjang.photo" />
                                                </td>
                                                <td class="cart-title first-row text-center">
                                                    <h5>{{keranjang.name}}</h5>
                                                </td>
                                                <td class="p-price first-row">{{formatPrice(keranjang.price)}}</td>
                                                <td class="delete-item"><a @click="removeItem(index)" href="#"><i class="material-icons">
                                                            close
                                                        </i></a></td>
                                            </tr>

                                        </tbody>
                                    </table>
                                </div>
                            </div>
                            <div class="col-lg-8">
                                <h4 class="mb-4">
                                    Informasi Pembeli:
                                </h4>
                                <div class="user-checkout">
                                    <div v-for="error in messageError" :key="error.id">
                                        <b>{{error}}</b>
                                    </div>
                                    <form>
                                        <div class="form-group">
                                            <label for="namaLengkap">Nama lengkap</label>
                                            <input v-model="customerInfo.name" type="text" class="form-control" id="namaLengkap" aria-describedby="namaHelp" placeholder="Masukan Nama">
                                        </div>
                                        <div class="form-group">
                                            <label for="namaLengkap">Email Address</label>
                                            <input v-model="customerInfo.email" type="email" class="form-control" id="emailAddress" aria-describedby="emailHelp" placeholder="Masukan Email">
                                        </div>
                                        <div class="form-group">
                                            <label for="namaLengkap">No. HP</label>
                                            <input v-model="customerInfo.number" type="text" class="form-control" id="noHP" aria-describedby="noHPHelp" placeholder="Masukan No. HP">
                                        </div>
                                        <div class="form-group">
                                            <label for="alamatLengkap">Alamat Lengkap</label>
                                            <textarea v-model="customerInfo.address" class="form-control" id="alamatLengkap" rows="3"></textarea>
                                        </div>
                                    </form>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="col-lg-4">
                        <div class="row">
                            <div class="col-lg-12">
                                <div class="proceed-checkout">
                                    <ul>
                                        <li class="subtotal">ID Transaction <span>#SH12000</span></li>
                                        <li class="subtotal mt-3">Subtotal <span>{{formatPrice(totalharga)}}</span></li>
                                        <li class="subtotal mt-3">Pajak <span>10% {{formatPrice(plusPajak)}}</span></li>
                                        <li class="subtotal mt-3">Total Biaya <span>{{formatPrice(totalBiaya)}}</span></li>
                                        <li class="subtotal mt-3">Bank Transfer <span>Mandiri</span></li>
                                        <li class="subtotal mt-3">No. Rekening <span>2208 1996 1403</span></li>
                                        <li class="subtotal mt-3">Nama Penerima <span>{{customerInfo.name}}</span></li>
                                    </ul>
                                    <a href="#" class="proceed-btn" @click="checkout()">I ALREADY PAID</a>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>
        <!-- Shopping Cart Section End -->

    </div>
    <FooterShayna />
</div>
</template>

<script>
import HeaderShayna from '@/components/HeaderShayna.vue'
import FooterShayna from '@/components/FooterShayna.vue'
import axios from 'axios'
export default {
    name: 'ShoppingCart',
    components: {
        HeaderShayna,
        FooterShayna
    },
    data() {
        return {
            keranjangUser: [],
            customerInfo: {
                name: '',
                email: '',
                number: '',
                address: ''
            },
            messageError: []
        }
    },
    methods: {
        removeItem(index) {
            this.keranjangUser.splice(index, 1)
            const parsed = JSON.stringify(this.keranjangUser)
            localStorage.setItem('keranjangUser', parsed)
        },

        formatPrice(value) {
            let val = (value / 1)
            return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
        },

        checkout() {
            let productIds = this.keranjangUser.map(function (product) {
                return product.id
            })

            let checkoutData = {
                name: this.customerInfo.name,
                email: this.customerInfo.email,
                number: this.customerInfo.number,
                address: this.customerInfo.address,
                transaction_total: this.totalharga,
                transaction_status: "PENDING",
                transaction_details: productIds,
            }

            // console.log(checkoutData)
            axios
                .post('http://localhost:8002/api/checkout', checkoutData)
                .then(() => this.$router.push('success'))
                .catch(err => (this.messageError = err.response.data.errors))

            localStorage.removeItem('keranjangUser')
        }
    },

    mounted() {
        if (localStorage.getItem('keranjangUser')) {
            try {
                this.keranjangUser = JSON.parse(localStorage.getItem('keranjangUser'));
            } catch (e) {
                localStorage.removeItem('keranjangUser');
            }
        }
    },
    computed: {
        totalharga() {
            return this.keranjangUser.reduce(function (items, data) {
                return items + data.price
            }, 0)
        },

        plusPajak() {
            return (this.totalharga * 10) / 100;
        },

        totalBiaya() {
            return this.totalharga + this.plusPajak
        }
    }
}
</script>
