/* eslint-disable vue/no-unused-vars */
/* eslint-disable vue/no-unused-vars */
<template>
  <div>
    <el-table v-loading="loading" :data="playlist" stripe>
      <el-table-column type="index" width="50" />
      <el-table-column label="封面" width="100">
        <template slot-scope="scope">
          <img :src="scope.row.picUrl" alt height="50">
        </template>
      </el-table-column>
      <el-table-column prop="name" label="名称" />
      <el-table-column prop="copywriter" label="描述" />
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" @click="onEdit(scope.row)">编辑</el-button>
          <el-button size="mini" type="danger" @click="onDel(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 确认删除对话框 -->
    <el-dialog title="提示" :visible.sync="delDialogVisible" width="30% ">
      <span>确定删除歌单吗</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="delDialogVisible = false">取消</el-button>
        <el-button type="primary" @click="doDel">确定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
// 跨域
// http://www.a.com http://www.b.com 域名不同
// http://www.a.com:8080 http://www.a.com:8081  端口号不同
// http://www.a.com http://news.a.com 主域和子域之间
// http://www.a.com https://www.a.com 协议不同
import { fetchList, del } from '@/api/playlist'
import scroll from '@/utils/scroll'
export default {
  data() {
    return {
      playlist: [],
      count: 50,
      loading: false, // 表示当前是否正在加载
      delDialogVisible: false,
      info: {}
    }
  },
  created() {
    this.getList()
  },
  mounted() {
    scroll.start(this.getList)
  },
  methods: {
    getList() {
      this.loading = true
      fetchList({
        start: this.playlist.lenth,
        count: this.count
      }).then(res => {
        console.log(res)
        this.playlist = this.playlist.concat(res.data)
        if (res.data.length < this.count) {
          scroll.end()
        }
        this.loading = false
      })
    },
    onEdit(row) {
      this.$router.push(`/playlist/edit/${row._id}`)
    },
    onDel(row) {
      this.delDialogVisible = true
      this.info.id = row._id
    },
    doDel() {
      del({
        id: this.info.id
      }).then((res) => {
        this.delDialogVisible = false
        if (res.data.deleted > 0) {
          this.playlist = []
          this.getList()
          this.$message({
            message: '删除成功',
            type: 'success'
          })
        } else {
          this.$message.error('删除失败')
        }
      })
    }
  }
}
</script>

<style scoped>

</style>
