<template>
    <div class="container" style="margin: 0.5%; margin-top: 2%;">
      <div slot="header">
          <b style="font-size: 27px;">Crud IG</b>
        </div>
        <el-row style="margin-top: 10px">
      <el-button style="float: left;" size="medium" type="success" @click="showModalAdd">+ Create</el-button>
      <!-- <el-button size="medium" type="primary" >批量启用</el-button>
      <el-button size="medium" type="warning" >批量禁用</el-button>
      <el-button  size="medium" type="danger" >批量删除</el-button> -->
    </el-row>
      <el-table
       :data="listData"
       v-loading="loading"
        border
        fit
        highlight-current-row
        style="margin-top: 1%"
        stripe
      >
        <el-table-column
          label="ID"
          prop="id"
          align="center"
          width="70"
        >
        <template slot-scope="{ row }">
          <span v-if="row.id">{{ row.id }}</span>
        </template>
        </el-table-column>
        <el-table-column
          label="User ID"
          prop="user_id"
          align="center"
          width="70"
        >
        <template slot-scope="{ row }">
          <span v-if="row.user_id">{{ row.user_id }}</span>
        </template>
        </el-table-column>
        <el-table-column
          label="Title"
          prop="title"
          align="center"
        >
        <template slot-scope="{ row }">
          <span style="text-align: start;" v-if="row.title">{{ row.title }}</span>
        </template>
        </el-table-column>
        <el-table-column
          label="Description"
          prop="bodyt"
          align="center"
        >
        <template slot-scope="{ row }">
          <span style="text-align: start;" v-if="row.body">{{ row.body }}</span>
        </template>
        </el-table-column>
        <el-table-column
          label="Edit"
          align="center"
          width="240px"
        >
          <template >
            <el-button  type="primary" size="mini" >
              Edit
            </el-button>
            <el-button  size="mini" type="danger">
              Delete
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- Modal create -->
      <el-dialog title="Create Data" :visible.sync="addDialog" center width="30%" @close="resetForm">
      <el-form label-position="right">
        <el-form-item prop="username" label="Name:" label-width="20%">
          <el-input
            type="text"
            placeholder="Name..."
            show-word-limit
          />
        </el-form-item>
        <el-form-item prop="nickname" label="Email:" label-width="20%">
          <el-input
            type="text"
            placeholder="email..."
          />
        </el-form-item>
        <el-form-item prop="nickname" label="Gender:" label-width="20%">
          <el-input
            type="text"
            placeholder="gender..."
          />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeAndClear()"> Cancel </el-button>
        <el-button type="primary">Add</el-button>
      </span>
    </el-dialog>
      <!-- End Modal create -->
    </div>
  </template>
  <script>
  export default {
    data() {
      return {
        listData: [],
        loading: false,
        addDialog: false
      }
    },
    mounted() {
        this.fetchData()
    },
    methods: {
      fetchData() {
      const getData = "https://gorest.co.in/public/v2/posts";
      this.$http.get(getData).then((res) => {
        this.listData = res.data;
        console.log(this.listData)
        this.loading = true;
        this.loading = false;
      });
    },
    showModalAdd() {
        this.addDialog = !this.addDialog
    },
    closeAndClear() {
      this.addDialog = false 
    }
    }
  }
  </script>
  <style>
   .container{
    text-align: center;
    margin-top: 3%;
   }
  </style>
  
  