<template>
  <div class="station-list">
    <div class="breadcrumb">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>设置</el-breadcrumb-item>
        <el-breadcrumb-item>站长列表</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div class="filter">
      <div class="l">
        <el-input placeholder="搜索关键词" icon="search" v-model="keywords" :on-icon-click="search"></el-input>
      </div>
    </div>
    <div class="main">
      <el-table :data="tableData">
        <el-table-column prop="customerName" label="平台名称"></el-table-column>
        <el-table-column prop="tripType" :formatter="typeFormat" label="类型"></el-table-column>
        <el-table-column prop="cityCodeName" label="地区"></el-table-column>
        <el-table-column prop="mainPerson" label="主要负责人"></el-table-column>
        <el-table-column prop="mainPhoneNumber" label="联系电话"></el-table-column>
        <el-table-column prop="mainWeChat" label="微信号"></el-table-column>
        <el-table-column prop="nickName" label="对接人"></el-table-column>
        <el-table-column label="操作" min-width="130">
          <template scope="scope">
            <el-button type="text" @click="detail(scope)">详情</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="pagination" v-show="pageCount>1">
      <el-pagination @current-change="pageIndexChange" :current-page="currentPage" :page-size="pageSize"
                     layout="total, prev, pager, next, jumper" :total="total">
      </el-pagination>
    </div>
  </div>
</template>

<script>

  import router from '../router'
  import {setPageIndex} from '../const'
  export default {
    name: 'stationList',
    data: function () {
      return {
        tableData: null,
        keywords: '',
        currentPage: 1,
        pageSize: 15,
        total: null,
        pageCount: 0
      }
    },
    watch: {
      '$route': 'routeChange'
    },
    methods: {
      routeChange: function () {
        console.log('routeChange', this.$route.params.index)
        this.currentPage = parseInt(this.$route.params.index)
        this.getTableData()
      },
      typeFormat: function (row, col) {
        if (row[col.property] === 0 || row[col.property] === '0') {
          return '未知'
        } else if (row[col.property] === 1 || row[col.property] === '1') {
          return '代理商'
        } else if (row[col.property] === 2 || row[col.property] === '2') {
          return '站长'
        } else if (row[col.property] === 3 || row[col.property] === '3') {
          return '高级站长'
        } else if (row[col.property] === 4 || row[col.property] === '4') {
          return '分社社长'
        } else if (row[col.property] === 5 || row[col.property] === '5') {
          return '联盟副会长'
        } else if (row[col.property] === 6 || row[col.property] === '6') {
          return '盟会长'
        }
      },
      getTableData: function () {
        this.$http.get('/v2/aut/crm/customer/list/master', {
          params: {
            pageSize: this.pageSize,
            pageIndex: this.currentPage,
            search: this.keywords
          }
        }).then(res => {
          console.log('获取站长列表', res)
          if (res.body.errMessage) {
            this.$message.error(res.body.errMessage)
          } else {
            this.tableData = res.body.data.data
            this.total = res.body.data.rowCount
            this.pageCount = res.body.data.pageCount
          }
        }).catch(res => {
          console.log('获取站长列表异常', res)
          this.$message.error('服务器繁忙！')
        })
      },
      search: function () {
        this.currentPage = 1
        this.getTableData()
      },
      pageIndexChange: function (val) {
        this.currentPage = val
        router.push({name: 'stationList', params: {index: val}})
      },
      detail: function (scope) {
        console.log(setPageIndex({
          name: 'stationPageIndex',
          value: this.currentPage
        }))
        router.push({name: 'stationDetail', params: {id: scope.row.id}})
      }
    },
    created: function () {
      console.log('mounted', this.$route.params.index)
      this.currentPage = parseInt(this.$route.params.index)
      this.getTableData()
    }
  }
</script>

<style lang="scss" scoped>

  .station-list{}

  .breadcrumb{
    padding: 20px;
    background: #fbfbfb;
  }
  .filter{
    margin-top: 15px;
    padding: 0 20px;
    .el-input{
      width: auto;
    }
    &:after{
      content: "";
      display: block;
      height: 0;
      width: 0;
      overflow: hidden;
      visibility: hidden;
      clear: both;
    }
    .l{
      float: left;
    }
    .r{
      float: right;
      a{
        color: inherit;
        text-decoration: none;
      }
      .uploader{
        display: inline-block;
        margin-left: 10px;
      }
    }
  }
  .main{
    padding: 0 20px;
    overflow: auto;
    margin-top: 15px;
  }

  .pagination{
    padding:20px;
    text-align: right;
  }
</style>
