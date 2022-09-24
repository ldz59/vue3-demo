<template>
  <div class="table-box">
    <!-- 标题 -->
    <div class="title">
      <h2>最简单的 CRUD Demo</h2>
    </div>
    <!-- 功能 -->
    <div class="query-box">
      <el-input class="query-input" v-model="queryInput" placeholder="请输入姓名搜索" @input="handleQueryName" />
      <div class="btn-list">
        <el-button type="primary" @click="handleAdd">增加</el-button>
        <el-button type="danger" @click="handleDelList" v-if="multipleSelection.length>0">删除</el-button>
      </div>
    </div>
    <!-- 表格 -->
    <el-table 
      ref="multipleTableRef"
      :data="tableData"
      style="width: 100%"
      @selection-change="handleSelectionChange"
      border
    >
      <el-table-column type="selection" width="55" />
      <el-table-column prop="id" label="编号" width="60" />
      <el-table-column prop="name" label="姓名" width="120" />
      <el-table-column prop="email" label="邮箱" width="120" />
      <el-table-column prop="phone" label="手机" width="120" />
      <el-table-column prop="state" label="状态" width="120" />
      <el-table-column prop="address" label="地址" width="300" />
      <el-table-column fixed="right" label="操作" width="120">
        <template #default="scope">
          <el-button style="color:#f56c6c"
          link
          type="primary"
          size="small"
          @click="handleRowDel(scope.row)"
        >删除</el-button>
          <el-button link type="primary" size="small" @click="handleEdit(scope.row)">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- dialog -->
    <el-dialog v-model="dialogFormVisible" :title="dialogType === 'add' ? '新增' : '编辑'">
      <el-form :model="tableForm">
        <el-form-item label="姓名" :label-width="100">
          <el-input v-model="tableForm.name" autocomplete="off" />
        </el-form-item>
        <el-form-item label="邮箱" :label-width="100">
          <el-input v-model="tableForm.email" autocomplete="off" />
        </el-form-item>
        <el-form-item label="电话" :label-width="100">
          <el-input v-model="tableForm.phone" autocomplete="off" />
        </el-form-item>
        <el-form-item label="状态" :label-width="100">
          <el-input v-model="tableForm.state" autocomplete="off" />
        </el-form-item>
        <el-form-item label="地址" :label-width="100">
          <el-input v-model="tableForm.address" autocomplete="off" />
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button type="primary" @click="dialogConfirm">确认</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script setup lang="ts">

  import { ref } from "vue";

  // 数据
  let queryInput = ref("")
  let tableData = ref([
    {
      id: '1',
      name: 'Tom1',
      email: '123@qq.com',
      phone: '13012345678',
      state: 'California',
      address: 'No. 189, Grove St, Los Angeles',
    },
    {
      id: '2',
      name: 'Tom2',
      email: '123@qq.com',
      phone: '13012345678',
      state: 'California',
      address: 'No. 189, Grove St, Los Angeles',
    },
    {
      id: '3',
      name: 'Tom3',
      email: '123@qq.com',
      phone: '13012345678',
      state: 'California',
      address: 'No. 189, Grove St, Los Angeles',
    },
    {
      id: '4',
      name: 'Tom4',
      email: '123@qq.com',
      phone: '13012345678',
      state: 'California',
      address: 'No. 189, Grove St, Los Angeles',
    },
    {
      id: '5',
      name: 'Tom5',
      email: '123@qq.com',
      phone: '13012345678',
      state: 'California',
      address: 'No. 189, Grove St, Los Angeles',
    },
  ])
  let tableDataCopy = Object.assign(tableData.value)
  let multipleSelection = ref([])
  let dialogFormVisible = ref(false)
  let tableForm = ref({
    name: '张三',
    email: '123@qq.com',
    phone: '13012345678',
    state: '在职',
    address: '福建省',
  })
  let dialogType = ref('add')


  // 方法
  // 搜索
  const handleQueryName = (val) => {
    // console.log(queryInput);
    // console.log(val);
    
    // 数据
    console.log(val.length);
    
    if(val.length > 0) {
      tableData.value = tableData.value.filter(item => (item.name).toLowerCase().match(val.toLowerCase()))
    } else {
      tableData.value = tableDataCopy
    }
    
  }
  // 删除一条
  const handleRowDel = ({id}) => {
    console.log(id)
    // 1. 通过ID获取到条目对应的 索引值
    let index = tableData.value.findIndex(item=>item.id === id)
    // 2. 通过索引值进行删除对应条目
    tableData.value.splice(index,1)
  }
  // 多条删除
  const handleDelList = () => {
    multipleSelection.value.forEach(id=>{
      handleRowDel({id})
    })
    // 清空
    multipleSelection.value = []
  }
  // 选中
  const handleSelectionChange = (val) => {
    // multipleSelection.value = val
    // 清空
    multipleSelection.value = []
    val.forEach(item => {
      multipleSelection.value.push(item.id)
    })
    console.log(multipleSelection.value);
    
  }
  // 新增
  const handleAdd = () => {
    dialogFormVisible.value = true
    tableForm.value = {}
  }
  // 编辑
  const handleEdit = (row) => {
    dialogFormVisible.value = true
    dialogType.value = "edit"
    tableForm.value = {...row}
  }
  // 确认
  const dialogConfirm = () => {
    dialogFormVisible.value = false
    // 1. 判断是新增还是编辑
    if (dialogType.value == "add") {
      // 1.拿到数据

      // 2.添加到表格
      tableData.value.push({
        id: (tableData.value.length + 1).toString(),
        ...tableForm.value,
      })
    } else {
      // 1.获取当前的这条的索引，
      let index = tableData.value.findIndex(item => item.id === tableForm.value.id)
      // console.log(index);
      tableData.value[index] = tableForm.value
      
      // 2.替换当前索引值对应的数据

    }
    
    console.log(tableData);
  }

</script>

<style scoped>
  .table-box {
    max-width: 800px;
    margin: 200px;
  }
  .title {
    text-align: center;
  }
  .query-box  {
    display: flex;
    justify-content: space-between;
    margin-bottom: 20px;
  }
  .query-input {
    width:200px
  }
</style>
