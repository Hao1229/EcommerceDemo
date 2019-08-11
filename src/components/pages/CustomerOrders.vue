<template>
    <div>
        <loading :active.sync="isLoading" :is-full-page="true">
        <template slot="before">I am</template>
        <template slot="default">
         <i class="fas fa-spinner fa-spin"></i>
        </template>
        <template slot="after">loading</template>
      </loading>
      <div class="row mt-4">
        <div class="col-md-4 mb-4" v-for="item in products" :key="item.id">
            <div class="card border-0 shadow-sm">
                <div style="height: 150px; background-size: cover; background-position: center" :style="{backgroundImage: `url(${item.imageUrl})`}"
                >
                </div>
                <div class="card-body">
                <span class="badge badge-secondary float-right ml-2">{{item.category}}</span>
                <h5 class="card-title">
                    <a href="#" class="text-dark">{{item.title}}</a>
                </h5>
                <p class="card-text">{{item.content}}</p>
                <div class="d-flex justify-content-between align-items-baseline">
                    <div class="h5" v-if="!item.price">{{item.origin_price}} 元</div>
                    <del class="h6" v-if="item.price">原價 {{item.origin_price}} 元</del>
                    <div class="h5" v-if="item.price">現在只要 {{item.price}} 元</div>
                </div>
                </div>
                <div class="card-footer d-flex">
                <button type="button" class="btn btn-outline-secondary btn-sm" @click="getProduct(item.id)">
                    <i class="fas fa-spinner fa-spin" v-if="status.loadingItem === item.id"></i>
                    查看更多
                </button>
                <button type="button" class="btn btn-outline-danger btn-sm ml-auto" @click="addtoCart(item.id)">
                    <i class="fas fa-spinner fa-spin" v-if="status.loadingItem === item.id"></i>
                    加到購物車
                </button>
                </div>
              </div>
            </div>
        </div>
        <!-- 分隔線 -->
        <div class="border-top border-info"></div>
        <!-- 購物車面板 -->
      <div class="row d-flex justify-content-center mt-5">
          <table class="table col-md-7">
          <thead>
            <tr>
              <th width="100"></th>
              <th>品名</th>
              <th width="120">數量</th>
              <th width="120">單價</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in cartsProducts" :key="item.id">
              <td><button class="btn btn-outline-danger" @click="removerCartItem(item.id)"><i class="fas fa-trash-alt"></i></button></td>
              <td>{{item.product.title}}
              <div class="text-success" v-if="item.coupon">
                已套用優惠卷
              </div>
              </td>
              <td>{{item.qty}}/{{item.product.unit}}</td>
              <td class="text-right">{{item.total | currency}}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="2"></td>
              <td class="text-right font-weight-bold">總計</td>
              <td class="text-right">{{carts.total | currency}}</td>
            </tr>
            <tr v-if="carts.final_total !== carts.total">
              <td colspan="2"></td>
              <td class="text-right font-weight-bold text-success">折扣價</td>
              <td class="text-right text-success">{{carts.final_total | currency}}</td>
            </tr>
          </tfoot>
        </table>
        <!-- 優惠碼面板 -->
        <div class="input-group mb-3 input-group-sm col-md-7">
          <input type="text" class="form-control" v-model="coupon_code" placeholder="請輸入優惠碼" >
          <div class="input-group-append">
            <button class="btn btn-outline-secondary" @click="addCouponCode">
              套用優惠碼
            </button>
          </div>
        </div>
      </div>
      <!-- 結帳面板 -->
      <div class="my-5 row justify-content-center" @submit.prevent="createOrder">
  <form class="col-md-6">
    <div class="form-group">
      <label for="useremail">Email</label>
      <input type="email" class="form-control" name="email" id="useremail" :class="{'is-invalid':errors.has('email')}"
        v-model="form.user.email" placeholder="請輸入 Email" v-validate="'required|email'">
      <span class="text-danger" v-if="errors.has('email')">{{errors.first('email')}}</span>
    </div>
    <div class="form-group">
      <label for="username">收件人姓名</label>
      <input type="text" class="form-control" name="name" id="username" :class="{'is-invalid':errors.has('name')}"
        v-model="form.user.name" placeholder="輸入姓名" v-validate="'required'">
      <span class="text-danger" v-if="errors.has('name')">姓名必須輸入</span>
    </div>
    <div class="form-group">
      <label for="usertel">收件人電話</label>
      <input type="tel" class="form-control" id="usertel" v-model="form.user.tel" placeholder="請輸入電話" name="phone"  :class="{'is-invalid':errors.has('phone')}" v-validate="'required'">
       <span class="text-danger" v-if="errors.has('phone')">請留下可連絡的電話</span>
    </div>
    <div class="form-group">
      <label for="useraddress">收件人地址</label>
      <input type="text" class="form-control" name="address" id="useraddress" v-model="form.user.address" :class="{'is-invalid':errors.has('address')}"
        placeholder="請輸入地址" v-validate="'required'">
      <span class="text-danger" v-if="errors.has('address')">地址欄位不得留空</span>
    </div>
    <div class="form-group">
      <label for="comment">留言</label>
      <textarea name="" id="comment" class="form-control" cols="30" rows="10" v-model="form.message"></textarea>
    </div>
    <div class="text-right">
      <button class="btn btn-danger">送出訂單</button>
    </div>
  </form>
</div>
        <!-- 單一產品模板 -->
        <div class="modal fade" id="productModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
          <div class="modal-dialog" role="document">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">{{product.title}}</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                  <span aria-hidden="true">&times;</span>
                </button>
              </div>
              <div class="modal-body">
                <img :src="product.imageUrl" class="img-fluid">
                <blockquote class="blockquote mt-3">
                  <p class="mb-0">{{product.content}}</p>
                  <footer class="blockquote-footer text-right text-nowrap">{{product.description}}</footer>
                </blockquote>
                <div class="d-flex justify-content-between align-items-baseline">
                  <div class="h4" v-if="!product.price">{{product.origin_price}} 元</div>
                  <div class="h6" v-if="product.price">原價 {{product.origin_price}} 元</div>
                  <div class="h4" v-if="product.price">現在只要 {{product.price}} 元</div>
                </div>
                <select name="" class="form-control mt-3" v-model="product.num">
                  <option :value="num" v-for="num in 10" :key="num">
                    選購{{num}}{{product.unit}}
                  </option>
                </select>
              </div>
              <div class="modal-footer">
                <div class="text-muted text-nowrap mr-3">
                  小計 <strong>{{product.num * product.price}}</strong>
                </div>
                <!-- <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button> -->
                <button type="button" class="btn btn-primary" @click="addtoCart(product.id, product.num)">加到購物車</button>
              </div>
            </div>
          </div>
        </div>
    </div>
</template>

<style>
  @import url("@fortawesome/fontawesome-free/css/all.css");
</style>

<script>
import $ from 'jquery'
export default {
  data () {
    return {
      products: [],
      product: {},
      carts: [],
      cartsProducts: [],
      isLoading: false,
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      },
      coupon_code: '',
      status: {
        loadingItem: ''
      }
    }
  },
  methods: {
    getProducts (page = 1) { // 預設值 page = 1 當未帶入參數時就會直接帶入 1
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/products/?page=${page}`
      const vm = this
      vm.isLoading = true
      this.$http.get(api).then(response => {
        console.log(response.data)
        vm.isLoading = false
        vm.products = response.data.products
      })
    },
    getProduct (id) {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/product/${id}`
      const vm = this
      vm.status.loadingItem = id
      this.$http.get(api).then(response => {
        console.log(response.data)
        vm.isLoading = false
        vm.product = response.data.product
        $('#productModal').modal('show')
        vm.status.loadingItem = ''
      })
    },
    addtoCart (id, qty = 1) {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`
      const vm = this
      const cart = {
        product_id: id,
        qty // 不需要寫成 qty:qty 它會直接把相同參數的變數直接帶值進去
      }
      vm.status.loadingItem = id
      this.$http.post(api, { data: cart }).then(response => {
        console.log(response.data)
        vm.status.loadingItem = ''
        vm.getCart()
        $('#productModal').modal('hide')
      })
    },
    getCart () {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`
      const vm = this
      vm.isLoading = true
      this.$http.get(api).then(response => {
        // console.log(response.data.data)
        vm.isLoading = false
        vm.carts = response.data.data
        vm.cartsProducts = response.data.data.carts
      })
    },
    removerCartItem (id) {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart/${id}`
      const vm = this
      vm.isLoading = true
      this.$http.delete(api).then(response => {
        console.log(response.data.data)
        vm.isLoading = false
        vm.getCart()
      })
    },
    addCouponCode () {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/coupon`
      const vm = this
      const coupon = {
        code: vm.coupon_code
      }
      vm.isLoading = true
      this.$http.post(api, {data: coupon}).then(response => {
        console.log(response.data)
        vm.isLoading = false
        vm.getCart()
      })
    },
    createOrder () {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/order`
      const vm = this
      const order = vm.form
      vm.isLoading = true
      this.$validator.validate().then((valid) => {
        if (valid) {
          this.$http.post(api, {data: order}).then(response => {
            console.log('訂單已完成', response.data)
            if (response.data.success) {
              this.$router.push(`/customer_checkout/${response.data.orderId}`)
            }
            vm.isLoading = false
          // vm.getCart()
          })
        } else {
          vm.isLoading = false
          console.log('欄位不完整')
        }
      })
    }
  },
  created () {
    this.getProducts()
    this.getCart()
  }
}
</script>
