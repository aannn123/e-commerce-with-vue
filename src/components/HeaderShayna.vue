<template>
<div>
    <!-- Header Section Begin -->
    <header class="header-section">
        <div class="header-top">
            <div class="container">
                <div class="ht-left">
                    <div class="mail-service">
                        <i class=" fa fa-envelope"></i> laravue-store@gmail.com
                    </div>
                    <div class="phone-service">
                        <i class=" fa fa-phone"></i> +62 8995398247
                    </div>
                </div>
            </div>
        </div>
        <div class="container">
            <div class="inner-header">
                <div class="row">
                    <div class="col-lg-2 col-md-2">
                        <div class="logo">
                            <a href="./index.html">
                                <img src="img/logo_website_shayna.png" alt="" />
                            </a>
                        </div>
                    </div>
                    <div class="col-lg-7 col-md-7"></div>
                    <div class="col-lg-3 text-right col-md-3">
                        <ul class="nav-right">
                            <li class="cart-icon" style="cursor:pointer">
                                Keranjang Belanja &nbsp;
                                <a href="#">
                                    <i class="icon_bag_alt"></i>
                                    <span>{{keranjangUser.length}}</span>
                                </a>
                                <div class="cart-hover">
                                    <div class="select-items">
                                        <table>
                                            <tbody v-if="keranjangUser.length > 0">
                                                <tr v-for="(keranjang, index) in keranjangUser" :key="index">
                                                    <td class="si-pic">
                                                        <img class="cart-photo" :src="keranjang.photo" alt="" />
                                                    </td>
                                                    <td class="si-text">
                                                        <div class="product-selected">
                                                            <p>{{formatPrice(keranjang.price)}} x {{keranjang.quantity}}</p>
                                                            <h6>{{keranjang.name}}</h6>
                                                        </div>
                                                    </td>
                                                    <td class="si-close">
                                                        <i @click="removeItem(index)" class="ti-close"></i>
                                                    </td>
                                                </tr>
                                            </tbody>
                                            <tbody v-else>
                                                <tr>
                                                    <td class="text-center">
                                                        Keranjang Kosong
                                                    </td>
                                                </tr>
                                            </tbody>
                                        </table>
                                    </div>
                                    <div v-if="keranjangUser.length > 0">
                                        <div class="select-total">
                                            <span>total:</span>
                                            <h5>{{formatPrice(totalHarga)}}</h5>
                                        </div>
                                        <div class="select-button">
                                            <router-link to="/cart" class="primary-btn view-card">
                                                <a href="#" style="color:#fff">
                                                    VIEW CARD
                                                </a>
                                            </router-link>
                                            <a href="#" class="primary-btn checkout-btn">CHECK OUT</a>
                                        </div>
                                    </div>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </header>
    <!-- Header End -->
</div>
</template>

<script>
export default {
    name: 'HeaderShayna',
    data() {
        return {
            keranjangUser: []
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
    computed: { //mengolah value yang berasal dari data atau berasal dari v-model
        totalHarga() {
            return this.keranjangUser.reduce(function (items, data) {
                // reduce = Mengakumulasikan atau mengurangi nilai berdasarkan elemen di dalam array
                return items + data.price
            }, 0)
        }
    }
}
// Sekilas computed properties dan methods hampir serupa dan bahkan mirip, lalu apakah perbedaan
// keduanya? Jika code diatas kita masukkan kedalam methods, maka hasil yang akan kita peroleh akan
// sama. Namun, bedanya adalah computed properti di-cache berdasarkan dependecies mereka, sehingga
// computed properties hanya akan menjalankan kembali fungsinya apabila beberapa
// dependencies (nilai yang dibutuhkan) terjadi perubahan. Itu berarti selama nilai 
// dari variable A, B dan Operator tidak mengalami perubahan maka function operasinya tidak akan
// dijalankan kembali. Sedangkan methods, setiap kali ada permintaan maka akan menjalankan kembali
// fungsinya.

// Lalu mengapa kita perlu menggunakan computed properties? Hal ini hanyalah masalah pilihan dan
// tergantung kebutuhan, dimana data yang perubahannya tidak sesering mungkin dan apalagi digunakan 
// berkali-kali sebaiknya disimpan di computed properties, sedangkan methods berlaku sebaliknya.
</script>

<style scoped>
.cart-photo {
    width: 80px;
    height: 80px;
}
</style>
