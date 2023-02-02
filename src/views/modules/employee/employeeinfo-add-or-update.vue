<template>
  <el-dialog
    :title="!dataForm.employeeid ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="名字" prop="name">
        <el-input v-model="dataForm.name" placeholder></el-input>
      </el-form-item>
      <el-form-item label="性别" prop="sex">
        <el-select v-model="dataForm.sex" placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="年龄" prop="age">
        <el-input v-model="dataForm.age" placeholder></el-input>
      </el-form-item>
      <el-form-item label="地址" prop="address">
        <el-input v-model="dataForm.address" placeholder></el-input>
      </el-form-item>
      <el-form-item label="电话" prop="phone">
        <el-input v-model="dataForm.phone" placeholder></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        employeeid: 0,
        name: "",
        sex: "",
        age: "",
        address: "",
        phone: ""
      },
      options: [
        {
          value: "男",
          label: "男"
        },
        {
          value: "女",
          label: "女"
        }
      ],
      dataRule: {
        name: [{ required: true, message: "不能为空", trigger: "blur" }],
        sex: [{ required: true, message: "不能为空", trigger: "blur" }],
        age: [{ required: true, message: "不能为空", trigger: "blur" }],
        address: [{ required: true, message: "不能为空", trigger: "blur" }],
        phone: [{ required: true, message: "不能为空", trigger: "blur" }]
      }
    }
  },
  methods: {
    init(id) {
      this.dataForm.employeeid = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields()
        if (this.dataForm.employeeid) {
          this.$http({
            url: this.$http.adornUrl(
              `/wms/employeeinfo/info/${this.dataForm.employeeid}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.name = data.employeeInfo.name
              this.dataForm.sex = data.employeeInfo.sex
              this.dataForm.age = data.employeeInfo.age
              this.dataForm.address = data.employeeInfo.address
              this.dataForm.phone = data.employeeInfo.phone
            }
          })
        }
      })
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/wms/employeeinfo/${
                !this.dataForm.employeeid ? "save" : "update"
              }`
            ),
            method: "post",
            data: this.$http.adornData({
              employeeid: this.dataForm.employeeid || undefined,
              name: this.dataForm.name,
              sex: this.dataForm.sex,
              age: this.dataForm.age,
              address: this.dataForm.address,
              phone: this.dataForm.phone
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false
                  this.$emit("refreshDataList")
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }
      })
    }
  }
}
</script>
