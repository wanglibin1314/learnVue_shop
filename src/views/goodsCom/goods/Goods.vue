/*
 * @Author: wangxin.leo
 * @Date: 2020-05-22 14:38:51
 * @Last Modified by: wangxin.leo
 * @Last Modified time: 2020-05-22 15:26:41
 */
<template>
  <div class="goods">
    <!--    面包屑导航-->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!--    卡片主体-->
    <el-card>
      <el-row :gutter="20">
        <el-col :span="8">
          <!--搜索框-->
          <el-input placeholder="请输入内容" v-model="query" clearable
            @clear="queryClick">
            <el-button slot="append" icon="el-icon-search" @click="queryClick">
            </el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <!--添加商品-->
          <el-button type="primary" @click="addGoodClick">添加商品</el-button>
        </el-col>
        <!--添加商品弹出框-->
      </el-row>
      <el-table :data="goodsData" style="width: 100%" stripe border>
        <el-table-column type="index" label="#" :index="index">
        </el-table-column>
        <el-table-column prop="goods_name" label="商品名称"
          :show-overflow-tooltip="true"></el-table-column>
        <el-table-column prop="goods_price" label="商品价格" width="100">
        </el-table-column>
        <el-table-column prop="goods_weight" label="商品重量" width="80">
        </el-table-column>
        <el-table-column prop="add_time" label="创建时间" width="150">
          <template slot-scope="scope">
            {{scope.row.add_time | dataFormat}}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="120">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit" size="mini"
              @click="console.log(scope.row.goods_id)"></el-button>
            <el-button type="danger" icon="el-icon-delete" size="mini"
              @click="deleteGoodFromId(scope.row.goods_id)"></el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination @size-change="goodsSizeChange"
        @current-change="goodsCurrentChange" :current-page="pageNum"
        :page-sizes="[1, 2, 5, 10]" :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
    </el-card>
  </div>
</template>

<script>
import { deleteGoodFromId, getGoods } from '../../../network/goodsCom/Goods'

export default {
  name: 'Goods',
  data () {
    return {
      // 商品查询条件
      query: '',
      pageNum: 1,
      pageSize: 10,
      total: 0,
      // 商品数据
      goodsData: []
    }
  },
  created () {
    this.getGoods()
  },
  methods: {
    index (val) {
      return (this.pageNum - 1) * this.pageSize + val + 1
    },
    // 清空搜索栏
    queryClick () {
      this.getGoods(this.query, 1)
    },
    // 添加商品
    addGoodClick () {
      this.$router.push('/goods/add')
    },
    // 商品可显示列
    goodsSizeChange (val) {
      this.pageSize = Number(val)
      this.getGoods()
    },
    // 商品选择页
    goodsCurrentChange (val) {
      this.pageNum = Number(val)
      this.getGoods()
    },
    // 获取商品数据(网络请求)
    getGoods (query = this.query, pageNum = this.pageNum, pageSize = this.pageSize) {
      getGoods(query, pageNum, pageSize).then(res => {
        console.log(res)
        if (res.data.meta.status !== 200) return this.$message.error(res.data.meta.msg)
        this.goodsData = res.data.data.goods
        this.total = res.data.data.total
      }).catch(err => {
        console.log(err)
      })
    },
    // 根据ID删除商品(网络请求)
    deleteGoodFromId (id) {
      this.$confirm('此操作将永久删除该参数, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        deleteGoodFromId(id).then(res => {
          if (res.data.meta.status !== 200) return this.$message.error(res.data.meta.msg)
          this.$message.success(res.data.meta.msg)
          this.getGoods()
        }).catch(err => {
          console.log(err)
        })
      }).catch((err) => {
        console.log(err)
      })
    }
  }
}
</script>

<style scoped>
</style>
