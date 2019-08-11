<template>
     <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li class="page-item" :class="{'disabled':getPagination.current_page === 1}"><a class="page-link" href="#" @click.prevent="judgePage(getPagination.current_page - 1)">Previous</a></li>
        <li class="page-item" v-for="page in getPagination.total_pages" :key='page' :class="{'active':getPagination.current_page === page}"><a class="page-link" href="#" @click.prevent="judgePage(page)">{{page}}</a></li>
        <li class="page-item" :class="{'disabled':getPagination.current_page === getPagination.total_pages}"><a class="page-link" href="#" @click.prevent="judgePage(getPagination.current_page + 1)">Next</a></li>
      </ul>
    </nav>
</template>

<script>
export default {
  props: ['getPagination'],
  data () {
    return {
      // Paginationdata: this.getPagination
    }
  },
  methods: {
    judgePage (page) {
      this.$emit('changePage', page)
    }
  }
}
</script>
