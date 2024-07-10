<template>
  <div class="common-layout">
    <el-container>
      <el-header><!-- 抽奖用户 列表 -->
        <el-text class="mx-1" type="success" size="large">随机抽取</el-text>

      </el-header>
      <el-main>
        <div class="demo">
          <!-- 中奖结束弹框 -->
          <el-card style="width: 60%;">
            <div class='addname'>
              <el-input v-model="addname" style="width: 1500px" :rows="2" type="textarea"
                placeholder="添加人员名字格式类型。例如：胡晓东、陈小、尘嚣、胡晓丽" />
              <el-button plain type="primary" @click="mockUserData">添加</el-button>
            </div>
            <div style="display:inline-block" lass="list">
              <p style="width:50px;display: inline-block;" v-for="item in state.list" :key="item.id">
                {{ item.name }} 
              </p>
              <!-- <span ref="moudole" v-for="item in state.list" :key="item.id"
                style="; display: inline-block;padding:20px;">
                <span style=" width: 50px;   box-sizing: border-box; display: inline-block;text-align: center;">
                  <span style="width: 50px;   box-sizing: border-box; display: inline-block;">
                    {{ item.name }}
                  </span>
                </span>
              </span> -->
            </div>

            <div>
              <div style="display: inline-block; font-weight: 1000;color: brown;" v-for="item in state.flist"
                :key="item.id"> {{item.name}} &nbsp; &nbsp; &nbsp;</div>
              <br>
              <br>
              <!-- <p style=" font-weight:  1000;color: brown中奖用户名称：{{ state.flist }}</p> -->
              <!-- 开始游戏按钮 -->
              <el-button plain type="primary" @click="startGameBtn" v-if="state.gameStatus === 'init'">开始抽奖</el-button>
              <el-button plain type="primary" disabled v-if="state.gameStatus === 'run'">进行中</el-button>
              <el-button plain type="primary" @click="restartGameBtn" v-if="state.gameStatus === 'end'">重新开始</el-button>
              <el-button plain type="primary" @click="selectBtn">确定随机的名单</el-button>
              <el-button plain type="info" @click="clearUserData">重置</el-button>
            </div>
          </el-card>
        </div>



        <!-- 选择窗口 -->
        <el-dialog v-model="dialogVisible" title="请选择需要随机的人名" width="500">
          <el-table @select-all="selectall" @select="selectEvent" :data="tableData" style="width: 100%">
            <el-table-column type="selection" width="55" />
            <el-table-column label="人员姓名" width="400">
              <template #default="scope">{{ scope.row.name }}</template>
            </el-table-column>
          </el-table>
          <br>
          <!-- 确定人数 -->
          <el-form-item label="确定选出的人数" :label-width="formLabelWidth">
            <el-input v-model="state.nums" placeholder="PS：选出的人数要小于总人数 " autocomplete="off" />
          </el-form-item>

          <template #footer>
            <div class="dialog-footer">
              <el-button @click="dialogVisible = false">取消</el-button>
              <el-button type="primary" @click="userFixData">
                确认
              </el-button>
            </div>
          </template>
        </el-dialog>
      </el-main>
    </el-container>
  </div>
</template>

<script lang="ts" setup>
import { reactive, onMounted, ref, computed } from 'vue'
import { ElNotification } from 'element-plus'
import { setCookie, getCookie, delCookie } from '../../unite/cookie'


const moudole = ref()
const state = reactive({
  dialogFormVisible:false,
  nums: '',
  list: [],
  flist: [],
  currentPerson: {
    name: '',
  },
  gameStatus: 'init',// init 初始化 状态  run 运行 状态 end 结束状态
  count: 100,
  displayCount: 0,
  openModal: false
})
//添加人名
let addname = ref('')
// mock 用户数据
const mockUserData = () => {
  if (addname.value !== '') {
    let data = []

    // data.push({
    //   name: ''
    // })
    data = addnameArr.value.map((x) => {
      return { name: x }
    })


    state.list = [...data, ...state.list]
    // console.log([...data, ...state.list], 'asdj')
    setCookie('perple', JSON.stringify(state.list), 24)
    // localStorage.setItem('perple', state.list)
    addname.value = ''
    ElNotification.success({
      message: '',
      title: '添加成功',

    })
  } else {
    ElNotification.error({
      title: '添加失败',
      message: '请输入人员名字',
    })
  }

}


// 延时 delay
const sleep = (delay) => new Promise((resolve) => setTimeout(resolve, delay))
//重置人员名字
const addnameArr = computed(() => {
  return addname.value.split("、")

})
const clearUserData = () => {
  state.list = []
  state.flist = []

  addname.value = ''
  delCookie('perple')

}

// 开始抽奖
const startGameBtn = async () => {

  let len = state.list.length
  let arrTwo = state.list
  for (var i2 = 0; i2 < len - 1; i2++) {
    var inde = parseInt(Math.random() * (len - i2));
    var tem = arrTwo[inde];
    arrTwo[inde] = arrTwo[len - i2 - 1];
    arrTwo[len - i2 - 1] = tem;
  }
  
  let n = state.count

  // for (let index = 0; index < state.nums; index++) {

  //   const obj = { id: index, name: arrTwo[index]["name"] }
  //   state.flist.push(obj)
  // } 
  state.gameStatus = 'run'
  state.openModal = true
  state.displayCount = n

  // let len = state.nums

  let temp = 0

  while (n--) {
    // state.displayCount = n
    await sleep(20)
    // const max = state.list.length;
    // const min = 0;
    // const randomIndex = Math.floor(Math.random() * (max - min)) + min;
    // state.currentPerson = state.list[randomIndex]
    // console.log('randomIndex', randomIndex)
    // console.log('state.currentPerson', state.currentPerson)
 
    let index =  0;
    for ( temp ; temp < state.list.length; temp++) {
      const obj = { id: temp, name: state.list[temp]["name"] }
        state.flist[index] = obj
      
      index++;

      if (temp == state.list.length - 1) {
        temp = -1;

      }
      if (index == state.nums) {
        break;
      }

    }

    temp++;

    // state.flist = []
    // let index = 0;
    // for (var i = temp; i < state.list.length - 1; i++) {
    //   const obj = { id: i, name: state.list[i]["name"] }
    //   state.flist.push(obj)
    //   index++;
    //   if (temp === state.list.length - 2) {
    //      temp = 0;  
    //   }
    //   if (index === state.nums) {
    //     break;
    //   }
    // }
    
  
}
  console.log('state.flist', state.flist)


  state.gameStatus = 'end'
  state.openModal = false
  console.log('state.list', state.list)
  // for (let index = 0; index < state.list.length; index++) {

  //   const obj = { id: index, name: state.list[index] }
  //   state.flist.push(obj)
  // }
  // let len = state.flist.length
  // let arr = state.flist
  // for (var i = 0; i < len - 1; i++) {
  //   var index = parseInt(Math.random() * (len - i));
  //   var temp = arr[index];
  //   arr[index] = arr[len - i - 1];
  //   arr[len - i - 1] = temp;
  // }
  // console.log("arr:", arr);

  // 
}

//选择人数queding抽奖
const userFixData = () => {
  state.list = selectState.value
  dialogVisible.value = false;

}
import { ElTable } from 'element-plus'

interface User {
  date: string
  name: string
  address: string
}
let tableData: User[] = [

]
//选择人数的按钮
let dialogVisible = ref(false)
const selectBtn = () => {
  let selectNumber = JSON.parse(getCookie('perple'))
  if (selectNumber != []) {
    dialogVisible.value = true;
    tableData = selectNumber

    // namestatus.value = getCookie('perple')
    console.log(selectNumber, 'selectNumber');


  }
}
//选择事件
let selectState = ref([])
const selectEvent = (value) => {
  if (value != []) {
    selectState.value = value
  }

  // console.log(state.list, 'hou');
  // console.log(getCookie('perple'), 'getS');
}
//选择所有事件
const selectall = (value) => {
  if (value != []) {
    selectState.value = value
    console.log(value, 'all');

  }
}
// 重新开始抽奖
const restartGameBtn = () => {
  state.flist = []
  startGameBtn()
}
onMounted(() => {
  // console.log(addnameArr.value.length, 'sdj');
  // mockUserData()
  if (JSON.parse(getCookie('perple'))) {
    state.list = JSON.parse(getCookie('perple'))

  }
  // const value = getCookie('perple')
  // console.log(moudole, 'moudole')


})
</script>
<style>
.mx-1 {
  text-align: center;
  font-size: 44px;

}

.el-header {
  display: flex;
  justify-content: center;
  align-items: center;
}

.demo {
  display: flex;
  justify-content: center;
  align-items: center;
}

.addname {
  display: flex;
  flex-wrap: nowrap;
  margin-bottom: 5px;
}

.el-textarea__inner {
  height: 17px;
}

.list {
  display: flex;
  flex-direction: column;
}
</style>
