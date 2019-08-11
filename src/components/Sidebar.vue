<template>
      <nav class="col-md-2 d-none d-md-block bg-light sidebar">
      <div class="sidebar-sticky">
        <h6 class="sidebar-heading d-flex justify-content-between align-items-center px-3 mt-4 mb-1 text-muted">
          <span>管理員</span>
          <a class="d-flex align-items-center text-muted" href="#">
            <span data-feather="plus-circle"></span>
          </a>
        </h6>
        <ul class="nav flex-column mb-2">
          <li class="nav-item">
            <router-link active-class="active" to="/admin/products" class="nav-link font-weight-bold"><i class="fas fa-box-open mr-1"></i>產品列表</router-link>
          </li>
          <li class="nav-item">
            <router-link active-class="active" to="/admin/orders" class="nav-link font-weight-bold"><i class="fas fa-list-alt mr-1"></i>訂單列表</router-link>
          </li>
          <li class="nav-item">
            <router-link active-class="active" to="/admin/coupons" class="nav-link font-weight-bold"><i class="fas fa-ticket-alt mr-1"></i>優惠卷</router-link>
          </li>
        </ul>
         <h6 class="sidebar-heading d-flex justify-content-between align-items-center px-3 mt-4 mb-1 text-muted">
          <span>模擬功能</span>
          <a class="d-flex align-items-center text-muted" href="#">
            <span data-feather="plus-circle"></span>
          </a>
        </h6>
        <ul class="nav flex-column mb-2">
          <li class="nav-item">
            <router-link  active-class="active" to="/customer_orders" class="nav-link font-weight-bold"><i class="fas fa-shopping-cart mr-1"></i>模擬訂單</router-link>
          </li>
        </ul>
      </div>
    </nav>
</template>

<script>
export default {
  data () {
    return {
    }
  },
  methods: {
    // toProduct () {
    //   let vm = this
    //   vm.isActive = true
    //   this.$router.push('/admin/products')
    // }
  }
}
</script>
