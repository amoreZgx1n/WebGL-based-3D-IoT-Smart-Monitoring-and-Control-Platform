<template>
  <div class="app-container">
    <el-col class="grid">
      <el-input
        v-model="input"
        placeholder="请输入查询内容"
        @input="search"
      ></el-input>
    </el-col>
    <br />
    <br />
    <br />
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
    >
      <el-table-column label="id" width="95" align="center">
        <template slot-scope="scope">
          {{ scope.$index }}
        </template>
      </el-table-column>
      <el-table-column label="date" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.date }}
        </template>
      </el-table-column>
      <el-table-column label="state" width="110" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.state }}</span>
        </template>
      </el-table-column>
      <el-table-column label="delay" width="110" align="center">
        <template slot-scope="scope">
          {{ scope.row.delay }}
        </template>
      </el-table-column>
      <el-table-column
        class-name="status-col"
        label="info"
        width="110"
        align="center"
      >
        <template slot-scope="scope">
          <el-tag :type="scope.row.info | statusFilter">{{
            scope.row.info
          }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="raw" width="200">
        <template slot-scope="scope">
          <span>{{ scope.row.raw }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        prop="created_at"
        label="humidity"
        width="200"
      >
        <template slot-scope="scope">
          <span>{{ scope.row.humidity }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        prop="created_at"
        label="temperature"
        width="200"
      >
        <template slot-scope="scope">
          <span>{{ scope.row.temperature }}</span>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  filters: {
    statusFilter (status) {
      const statusMap = {
        published: 'success',
        draft: 'gray',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data () {
    return {
      list: null,
      listData: null,
      listLoading: true,
      input: ''
    }
  },
  created () {
    ;(async () => {
      this.listLoading = true
      this.listData = await this.fetchData('lora-p2p', 300)
      this.listData.forEach(row => {
        row.date = new Date(row.date).toLocaleString()
      })
      this.list = this.listData
      this.listLoading = false
    })()
  },
  methods: {
    async fetchData (tableName = 'lora-p2p', limit = null) {
      const url = new URL('http://101.132.165.23:3000/get-table')
      url.searchParams.set('table', tableName)
      if (limit) {
        url.searchParams.set('limit', limit)
      }
      const response = await axios.get(url)
      return response.data.result
    },
    async search () {
      const input = this.input
      this.listLoading = true
      if (input) {
        this.list = this.listData.filter(row => row.date.includes(input))
      } else {
        this.list = this.listData
      }
      this.listLoading = false
    }
  }
}
</script>
