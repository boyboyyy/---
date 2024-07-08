<template>
  <div class="common-layout">
    <el-container>
      <el-header><!-- 抽奖用户 列表 -->
        <el-text class="mx-1" type="success" size="large">随机抽取幸运观众,汗流浃背了吧。</el-text>

      </el-header>
      <el-main>
        <div class="demo">
          <!-- 中奖结束弹框 -->
          <el-card style="max-width: 1200px">
            <div class='addname'> <el-input v-model="addname" placeholder="请添加抽奖人员姓名"></el-input>
              <el-button plain type="primary" @click="mockUserData">添加</el-button>
            </div>

            <div v-for="item in state.list" :key="item.id" style="display: inline-block;padding:20px">
              <div style="display: inline-block;text-align: center;">
                <div>
                  {{ item.name }}
                </div>

              </div>
            </div>
            <div>
              <p>中奖用户名称：{{ state.currentPerson?.name }}</p>
              <!-- 开始游戏按钮 -->
              <el-button plain type="primary" @click="startGameBtn" v-if="state.gameStatus === 'init'">开始抽奖</el-button>
              <el-button plain type="primary" disabled v-if="state.gameStatus === 'run'">进行中</el-button>
              <el-button plain type="primary" @click="restartGameBtn" v-if="state.gameStatus === 'end'">重新开始</el-button>
              <el-button plain type="info" @click="clearUserData">重置</el-button>

            </div>
          </el-card>
        </div>




      </el-main>
    </el-container>
  </div>
</template>

<script setup>
import { reactive, onMounted, ref } from 'vue'
import { ElNotification } from 'element-plus'




const state = reactive({
  list: [],
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
    data.push({
      name: addname.value
    })

    state.list = [...data, ...state.list]
    console.log(state.list)
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
const clearUserData = () => {
  state.list = []
  addname.value = ''


}

// 开始抽奖
const startGameBtn = async () => {

  let n = state.count
  while (n--) {
    state.displayCount = n
    await sleep(20)
    const max = state.list.length;
    const min = 0;
    const randomIndex = Math.floor(Math.random() * (max - min)) + min;
    state.currentPerson = state.list[randomIndex]
    console.log('randomIndex', randomIndex)
    console.log('state.currentPerson', state.currentPerson)
    state.gameStatus = 'run'
  }
  state.gameStatus = 'end'
  state.openModal = true
}

const afterCloseModal = () => {
  state.openModal = false
}

// 重新开始抽奖
const restartGameBtn = () => {
  startGameBtn()
}
onMounted(() => {
  // mockUserData()
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
}
</style>
