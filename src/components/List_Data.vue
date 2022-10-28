<template>
  <div class="container" style="margin: 0.5%; margin-top: 2%;">
    <div slot="header">
        <b style="font-size: 27px;">Crud IG</b>
      </div>
      <el-row style="margin-top: 10px">
    <el-button style="float: left;" size="medium" type="success" @click="showModalAdd()">+ Create</el-button>
  </el-row>
    <el-table
     :key="tableKey"
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
        label="User Name"
        prop="name"
        align="center"
      >
      <template slot-scope="{ row }">
        <span v-if="row.name">{{ row.name }}</span>
      </template>
      </el-table-column>
      <el-table-column
        label="Gender"
        prop="gender"
        align="center"
      >
      <template slot-scope="{ row }">
        <span style="text-align: start;" v-if="row.gender">{{ row.gender }}</span>
      </template>
      </el-table-column>
      <el-table-column
        label="Email"
        prop="email"
        align="center"
      >
      <template slot-scope="{ row }">
        <span style="text-align: start;" v-if="row.email">{{ row.email }}</span>
      </template>
      </el-table-column>
      <el-table-column
        label="Status"
        prop="status"
        align="center"
      >
      <template slot-scope="{ row }">
        <span style="text-align: start;" v-if="row.status">{{ row.status }}</span>
      </template>
      </el-table-column>
      <el-table-column
        label="Edit"
        align="center"
        width="240px"
      >
        <template slot-scope="{ row }">
          <el-button @click="showModalEdit(row)" type="primary" size="mini" >
            Edit
          </el-button>
          <el-button @click="deleteUser(row)" size="mini" type="danger">
            Delete
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <br>
    <!-- Modal Edit -->
    <el-dialog title="Edit Data" :visible.sync="editDialog" center width="30%" @close="resetValidate('selectRow')">
    <el-form label-position="right" ref="selectRow" :rules="valideInfo" :model="selectRow">
      <el-form-item prop="name" label="Name:" label-width="20%">
        <el-input
          v-model="selectRow.name"
          type="text"
          placeholder="Name..."
          show-word-limit
        />
      </el-form-item>
      <el-form-item prop="email" label="Email:" label-width="20%">
        <el-input
          v-model="selectRow.email"
          type="text"
          placeholder="email..."
        />
      </el-form-item>
      <el-form-item prop="gender" label="Gender:" label-width="20%">
        <el-radio  v-model="selectRow.gender" label="male">Male</el-radio>
        <el-radio v-model="selectRow.gender" label="female">Female</el-radio>
      </el-form-item>
      <el-form-item prop="status" label="Status:" label-width="20%">
          <el-switch
          style="display: block; margin-top: 10px;"
          :value="selectRow.status === 'active' ? true : false"
          active-color="#13ce66"
          inactive-color="#ff4949"
          @change="changeEditStatus()"
          >
          </el-switch>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="editDialog = false"> Cancel </el-button>
      <el-button v-if="editDialog" type="primary" @click="getEditData()">Update</el-button>
    </span>
  </el-dialog>
    <!-- End Modal Edit -->
    <!-- Modal create -->
    <el-dialog title="Create Data" :visible.sync="addDialog" center width="30%" @close="resetValidate('addData')">
    <el-form ref="addData" :rules="valideInfo" :model="addData" label-position="right">
      <el-form-item prop="name" label="Name:" label-width="20%">
        <el-input
          v-model="addData.name"
          type="text"
          placeholder="Name..."
          show-word-limit
        />
      </el-form-item>
      <el-form-item prop="email" label="Email:" label-width="20%">
        <el-input
          v-model="addData.email"
          type="email"
          placeholder="email..."
        />
      </el-form-item>
      <el-form-item prop="gender" label="Gender:" label-width="20%">
        <el-radio  v-model="addData.gender" label="male">Male</el-radio>
        <el-radio v-model="addData.gender" label="female">Female</el-radio>
      </el-form-item>
      <el-form-item prop="status" label="Status:" label-width="20%">
          <el-switch
          style="display: block; margin-top: 10px;"
          :value="addData.status === 'active' ? true : false"
          active-color="#13ce66"
          inactive-color="#ff4949"
          @change="changeStatus()"
          >
          </el-switch>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="addDialog = false"> Cancel </el-button>
      <el-button type="primary" @click="createData()">Add</el-button>
    </span>
  </el-dialog>
    <!-- End Modal create -->
  </div>
</template>
<script>
import  axios  from 'axios'
export default {
  data() {
    const name = (rule, value, callback) => {
    if (value === '') {
      callback(new Error('name can not blank'))
    } else {
      callback()
    }
  }
    const email = (rule, value, callback) => {
    if (value === '') {
      callback(new Error('email can not blank'))
    } else {
      callback()
    }
  }
    return {
      listData: [],
      loading: false,
      addDialog: false,
      editDialog: false,
      addData: {
          name: '',
          email: '',
          gender: '',
          status: 'active'
      },
      dataValue: '',
      tableKey: 0,
      selectRow: {},
      valideInfo: {
        name: [{ required: true, trigger: 'blur', validator: name }],
        email: [{ required: true, trigger: 'blur', validator: email }],
      },
    }
  },
  created() {
      this.fetchData()
  },
  methods: {
    fetchData() {
    const getData = "https://gorest.co.in/public/v2/users?page=1&per_page=10";
    this.$http.get(getData).then((res) => {
      this.listData = res.data;
    });
  },
  updateData(input) {
    this.dataValue = input.replace(/\s+/g, '')
  },
 async createData() {  
    this.$refs.addData.validate( async valid => {
      if (valid) {
       await axios
      .post(
        `https://gorest.co.in/public/v2/users`, 
        { email: this.addData.email, name: this.addData.name, gender: this.addData.gender, status: this.addData.status },
            {
                headers: {
                    Authorization: 'Bearer 437bbabdbdd45a10dc1f92bfeec09c9715893e67426eab77b85a7d20032b088b'
                }
            }
      ).then((res) => {
        this.addData = res.data
      })
      this.fetchData()
      this.addDialog = !this.addDialog
      }
    })
  },
  changeStatus() {
   this.addData.status === 'active' ? this.addData.status = 'inactive' : this.addData.status = 'active'
  },
  changeEditStatus() {
   this.selectRow.status === 'active' ? this.selectRow.status = 'inactive' : this.selectRow.status = 'active'
  },
  showModalAdd() {
      this.addDialog = !this.addDialog
  },
  showModalEdit(row) {
      this.editDialog = !this.editDialog
      this.selectRow = row
  },

  getEditData() {
      this.$refs.selectRow.validate( async valid => {
          if (valid) {
      axios
      .put(
        `https://gorest.co.in/public/v2/users/` + this.selectRow.id,
        { email: this.selectRow.email, name: this.selectRow.name, gender: this.selectRow.gender, status: this.selectRow.status },
            {
                headers: {
                    Authorization: 'Bearer 437bbabdbdd45a10dc1f92bfeec09c9715893e67426eab77b85a7d20032b088b'
                }
            }
      ).then((res) => {
        console.log(res)
        this.fetchData()
      })
      this.editDialog = !this.editDialog
    }
    })
    },
  closeAndClear(row) {
    this.addDialog = false 
    this.editDialog = false 
    this.selectRow = row
   },
   resetValidate(form) {
    this.addDialog = false
    this.editDialog = false
    this.addData.name = ''
    this.addData.email = ''
    this.$refs[form].resetFields()
  },
   deleteUser(row) {
      axios
    .delete(
      `https://gorest.co.in/public/v2/users/${row.id}`,
          {
              headers: {
                  Authorization: 'Bearer 437bbabdbdd45a10dc1f92bfeec09c9715893e67426eab77b85a7d20032b088b'
              }
          }
    ).then((res) => {
      console.log(res)
      this.fetchData()
  })
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

