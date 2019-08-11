<template>
    <div>
        <loading :active.sync="isLoading" :is-full-page="true">
        <template slot="before">I am</template>
        <template slot="default">
         <i class="fas fa-spinner fa-spin"></i>
        </template>
        <template slot="after">loading</template>
      </loading>
        <table class="table mt-5">
            <thead>
                <tr>
                <th width="140">購物時間</th>
                <th>Email</th>
                <th>購買款項</th>
                <th width="130">應付金額</th>
                <th width="120">是否付款</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in orders" :key="item.id">
                <td>{{item.paid_date | date}}</td>
                <td>{{item.user.email}}</td>
                <td v-for="(product, i) in item.products" :key="i" class="d-flex flex-column">
                  {{product.product.title}} 數量：{{product.product.num}} {{product.product.unit}}
                </td>
                <td class="text-right">{{item.total | currency}}</td>
                <td class="text-success" v-if="item.is_paid">已付款</td>
                <td class="text-muted" v-if="!item.is_paid">尚未付款</td>
                </tr>
            </tbody>
        </table>
        <pagination :getPagination="pagination" @changePage="getOrder"></pagination>
    </div>
</template>

<script>
import pagination from './Pagination'
export default {
  components: {
    pagination
  },
  data () {
    return {
      isLoading: false,
      orders: [],
      order: {},
      pagination: []
    }
  },
  methods: {
    getOrder (page = 1) {
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/orders/?page=${page}`
      const vm = this
      vm.isLoading = true
      this.$http.get(api).then(response => {
        console.log(response)
        vm.isLoading = false
        vm.orders = response.data.orders
        vm.pagination = response.data.pagination
      })
    }
  },
  created () {
    this.getOrder()
  }
}
</script>
