<template>
    <div>
        <loading :active.sync="isLoading" :is-full-page="true">
        <template slot="before">I am</template>
        <template slot="default">
         <i class="fas fa-spinner fa-spin"></i>
        </template>
        <template slot="after">loading</template>
      </loading>
      <div class="d-flex justify-content-end mt-3">
        <button class="btn btn-primary" @click="openModal(true)">建立新的優惠卷</button>
      </div>
        <table class="table mt-5">
  <thead>
    <tr>
      <th>名稱</th>
      <th>折扣百分比</th>
      <th>到期日</th>
      <th>是否啟用</th>
      <th>編輯</th>
      <th>刪除</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="item in couponData" :key="item.title">
      <th>{{item.title}}</th>
      <td>{{item.percent}}%</td>
      <td>{{item.due_date}}</td>
      <td class="text-success" v-if="item.is_enabled">啟用</td>
      <td class="text-muted" v-if="!item.is_enabled">未啟用</td>
      <td><button class="btn btn-primary btn-sm" @click="openModal(false, item)">編輯</button></td>
      <td><button class="btn btn-danger btn-sm" @click="opendelModal(item)">刪除</button></td>
    </tr>
  </tbody>
</table>
<!-- 建立優惠卷視窗 -->
    <div class="modal fade" id="couponModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">優惠卷製作</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="form-group">
              <label for="title">標題</label>
              <input type="text" id="title" placeholder="輸入新優惠卷標題" class="form-control" v-model="couponDetail.title">
            </div>
            <div class="form-group">
              <label for="code">優惠碼</label>
              <input type="text" id="code" placeholder="設定優惠卷代碼" class="form-control" v-model="couponDetail.code">
            </div>
            <div class="form-group">
              <label for="date">到期日</label>
              <input type="date" id="date" placeholder="設定優惠卷到期日" class="form-control" v-model="couponDetail.due_date">
            </div>
            <div class="form-group">
              <label for="percent">折扣百分比</label>
              <input type="number" id="percent" placeholder="設定優惠卷折扣" class="form-control" v-model="couponDetail.percent">
            </div>
            <div class="form-group">
              <input type="checkbox" id="isenabled" v-model="couponDetail.is_enabled">
              <label for="isenabled">是否啟用</label>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">取消</button>
            <button type="button" class="btn btn-primary" @click="createCoupon">確認優惠卷</button>
          </div>
        </div>
      </div>
    </div>
    <!-- 刪除模板 -->
    <div
      class="modal fade"
      id="delCouponModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content border-0">
          <div class="modal-header bg-danger text-white">
            <h5 class="modal-title" id="exampleModalLabel">
              <span>刪除優惠卷</span>
            </h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            是否刪除
            <strong class="text-danger">{{ couponDetail.title }}</strong> 優惠卷(刪除後將無法恢復)。
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
            <button type="button" class="btn btn-danger" @click="delCoupon">確認刪除</button>
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
      couponDetail: {},
      couponData: [],
      isLoading: false,
      isNew: false
    }
  },
  methods: {
    createCoupon () {
      let api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/coupon`
      let httpMethod = 'post'
      const vm = this
      if (!vm.isNew) {
        api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/coupon/${vm.couponDetail.id}`
        httpMethod = 'put'
      }
      $('#couponModal').modal('show')
      vm.isLoading = true
      this.$http[httpMethod](api, {data: vm.couponDetail}).then(response => {
        console.log(response.data)
        vm.isLoading = false
        vm.getCoupon()
        $('#couponModal').modal('hide')
      })
    },
    getCoupon (page = 1) {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/coupons?page=${page}`
      const vm = this
      vm.isLoading = true
      this.$http.get(api).then(response => {
        console.log(response.data)
        vm.couponData = response.data.coupons
        vm.isLoading = false
      })
    },
    openModal (isNew, item) {
      if (isNew) {
        const vm = this
        vm.couponDetail = {}
        vm.isNew = true
      } else {
        this.couponDetail = Object.assign({}, item)
        this.isNew = false
      }
      $('#couponModal').modal('show')
    },
    delCoupon () {
      const vm = this
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/coupon/${vm.couponDetail.id}`
      vm.isLoading = true
      this.$http.delete(api).then(response => {
        console.log(response.data)
        vm.isLoading = false
        vm.getCoupon()
        $('#delCouponModal').modal('hide')
      })
    },
    opendelModal (item) {
      this.couponDetail = Object.assign({}, item)
      $('#delCouponModal').modal('show')
    }
  },
  created () {
    this.getCoupon()
  }
}
</script>
