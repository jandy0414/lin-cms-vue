<template>
  <div>
    <!-- 列表页面 -->
    <div class="container" v-if="!showEdit">
      <div class="header">
        <div class="title">用户列表</div>
      </div>
      <!-- 表格 -->
      <lin-table :tableColumn="tableColumn"
                :tableData="tableData"
                :operate="operate"
                @handleEdit="handleEdit"
                @handleDelete="handleDelete"
                @row-click="rowClick"
                v-loading="loading"></lin-table>
    </div>

    <!-- 编辑页面 -->
    <book-edit
      v-else
      @editClose="editClose"
      :editBookID="editBookID"
    ></book-edit>

  </div>
</template>

<script>
import book from '@/lin/models/book'
import LinTable from '@/base/table/lin-table'
import BookEdit from './BookEdit'

export default {
  components: {
    LinTable,
    BookEdit,
  },
  data() {
    return {
      tableColumn: [{ prop: 'title', label: '书名' }, { prop: 'author', label: '作者' }],
      tableData: [],
      operate: [],
      showEdit: false,
      editBookID: 1,
    }
  },
  async created() {
    this.loading = true
    this.getBooks()
    this.operate = [{ name: '编辑', func: 'handleEdit', type: 'edit' }, { name: '删除', func: 'handleDelete', type: 'del' }]
    this.loading = false
  },
  methods: {
    async getBooks() {
      const books = await book.getBooks()
      this.tableData = books
    },
    handleEdit(val) {
      this.showEdit = true
      this.editBookID = val.row[val.index].id
    },
    handleDelete(val) {
      this.$confirm('此操作将永久删除该图书, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning',
      }).then(async () => {
        const res = await book.delectBook(val.row[val.index].id)
        if (res.error_code === 0) {
          this.getBooks()
          this.$message({
            type: 'success',
            message: `${res.msg}`,
          })
        }
      })
    },
    rowClick() {

    },
    editClose() {
      this.showEdit = false
    },
  },
}
</script>

<style lang="scss" scoped>
@import "~assets/styles/variable.scss";
.container {
  padding: 0 30px;
  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    .title {
      height: 59px;
      line-height: 59px;
      color: $parent-title-color;
      font-size: 16px;
      font-family: PingFangSC-Medium;
      font-weight: 500;
    }
  }
  .pagination {
    display: flex;
    justify-content: flex-end;
    margin: 20px;
  }
}

</style>
