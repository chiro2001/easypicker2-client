<template>
  <div class="task-panel">
    <div class="pc-nav">
      <div class="nav">
        <!-- LOGO -->
        <div class="logo">
          <router-link to="/">
            <img style="height: 40px;width: 170px;" src="https://img.cdn.sugarat.top/easypicker/EasyPicker.png"
              alt="logo" />
          </router-link>
        </div>
        <nav>
          <div class="nav-item" v-for="(n, idx) in pcNavs" :key="idx" @click="handleNav(idx)">
            {{ n.title }}
          </div>
          <!-- TODO:重新加导航内容 -->
          <!-- 底部导航栏 -->
        </nav>
      </div>
    </div>

    <div v-loading="isLoadingData" element-loading-text="Loading..." class="panel tc" v-if="
      sid
    ">
      {{ sid }}
      {{ displayData }}

      <img v-bind:src="displayData.image.link" v-if="displayData.image.link" />

      <!-- <h1 class="name">
        {{ taskInfo.name }}
      </h1>
      <template v-if="taskMoreInfo.tip">
        <el-divider>⚠️ 注意事项 ⚠️</el-divider>
        <Tip>
          <div class="tip-wrapper">
            <p v-for="(t, i) in taskMoreInfo.tip.split('\n')" :key="i">{{ t.replace(/\s/g,'&nbsp;') }}</p>
          </div>
        </Tip>
      </template>
      <template v-if="ddlStr">
        <el-divider>截止时间</el-divider>
        <h2 class="ddl">
          {{ ddlStr }}
          <span>
            {{
                isOver
                  ? '已经结束'
                  : waitTimeStr
            }}
          </span>
        </h2>
      </template>

      <div v-if="!ddlStr || !isOver">
        <el-divider>必要信息填写</el-divider>
        <div class="infos">
          <InfosForm :infos="infos" :disabled="disableForm"></InfosForm>
        </div>
        <el-upload style="max-width: 400px; margin: 0 auto;" :drag="!isMobile" action="" ref="fileUpload"
          :on-change="handleChangeFile" :before-remove="
            handleRemoveFile
          " :on-exceed="handleExceed" :auto-upload="false" multiple :limit="limitUploadCount" :file-list="fileList">
          <el-button v-if="isMobile" type="primary">选择文件</el-button>
          <template v-else>
            <el-icon class="el-icon--upload">
              <upload-filled />
            </el-icon>
            <div class="el-upload__text">
              将文件拖于此处 or <em>直接选择文件</em>
            </div>
          </template>
          <template #tip>
            <div class="p10" v-show="!!calculateMd5Count">
              <tip>还有 {{ calculateMd5Count }} 个文件正在生成校验信息，请稍等(1G通常需要20s)</tip>
            </div>
          </template>
        </el-upload>
        <div class="p10">
          <el-button v-if="isWithdraw" size="default" @click="startWithdraw" type="warning"
            :disabled="!allowWithdraw || !!calculateMd5Count">一键撤回</el-button>
          <el-button v-else size="default" @click="submitUpload" type="success"
            :disabled="!allowUpload || !!calculateMd5Count">提交文件</el-button>
          <el-button @click="checkSubmitStatus" size="default">查询提交情况</el-button>
        </div>
        
        <div class="p10 option-tips">
          <template v-if="isWithdraw">
            <tip>① 须保证选择的文件与提交时的文件一致<br /> ② 填写表单信息一致 <br /> ③ 完全一模一样的文件的提交记录（内容md5+命名），将会一次性全部撤回</tip>
          </template>
          <template v-else>
            <tip>① 选择完文件，点击 ”提交文件“即可 <br />
              ② <strong>选择大文件后需要等待一会儿才展示处理</strong>
              <template v-if="taskMoreInfo.template"><br /> ③ <strong>
                  <el-button type="text" style="color: #85ce61" size="small" @click="downloadTemplate">右下角可 “查看提交示例”
                  </el-button>
                </strong></template>
            </tip>
          </template>
        </div>
        <div class="withdraw">
          <el-button type="text" style="color: #85ce61" v-if="taskMoreInfo.template" size="small"
            @click="downloadTemplate">查看提交示例</el-button>
          <el-button v-if="isWithdraw" @click="isWithdraw = false" size="small" type="text">正常提交</el-button>
          <el-button v-else size="small" @click="isWithdraw = true" type="text">我要撤回</el-button>
        </div>
      </div> -->
    </div>
    <!-- 无效任务 -->
    <!-- <div class="panel tc" v-else>
      <h1 class="name">
        {{ taskInfo.name }}
      </h1>
    </div> -->
    <!-- <LinkDialog v-model:value="showLinkModel" title="示例文件下载链接" :link="templateLink"></LinkDialog> -->
  </div>
</template>
<script lang="ts" setup>
import {
  ElMessage, ElMessageBox,
} from 'element-plus'
import {
  computed,
  onMounted,
  onUnmounted,
  reactive,
  ref,
} from 'vue'
import {
  useRoute,
  useRouter,
} from 'vue-router'
import { useStore } from 'vuex'
import {
  FileApi,
  PeopleApi,
  TaskApi,
} from '@/apis'
import { parseInfo } from '@/utils/stringUtil'

const $store = useStore()
const isMobile = computed(() => $store.getters['public/isMobile'])
// 顶部导航
const $router = useRouter()
const $route = useRoute()
const pcNavs = reactive([
  {
    title: '我也要收集',
    path: '/',
  },
])
const handleNav = (idx: number) => {
  $router.push({
    path: pcNavs[idx].path,
  })
}

interface StudentData {
  sid: number,
  name: string
}

interface ImageResult {
  link: string,
  mimeType: string
}

interface DisplayData {
  relativeData: StudentData,
  image: ImageResult
}

// 任务基本信息展示
const taskInfo = reactive<TaskApiTypes.TaskInfo>({ name: '', category: '' })
const taskMoreInfo = reactive<Partial<TaskApiTypes.TaskInfo>>({})
const sid = ref('')

const displayData = reactive<DisplayData>({
  relativeData: {
    sid: 0,
    name: ''
  },
  image: {
    link: null,
    mimeType: null
  }
});

const peopleName = ref('')
const confirmPeopleName = () => ElMessageBox.prompt(
  '请输入你的姓名',
  '姓名核验',
  {
    confirmButtonText: '确定',
    cancelButtonText: '取消',
    draggable: true,
  },
)
  .then(({ value }) => value)
  .catch(() => {
    ElMessage.info('取消')
    return ''
  })

const isLoadingData = ref(false)
const defaultTaskKey = ref(null)

const isEqualInfos = (a: InfoItem[] = [], b: InfoItem[] = []) => {
  if (a.length !== b.length) {
    return false
  }
  return a.every((v, i) => (v.type === b[i].type && v.text === b[i].text && isEqualInfos(v.children, b[i].children)))
}
// const refreshTaskMoreInfo = (hot = false) => {
//   TaskApi.getTaskMoreInfo(
//     defaultTaskKey.value,
//   ).then((res) => {
//     Object.assign(
//       taskMoreInfo,
//       res.data,
//     )
//     if (!isEqualInfos(infos, parseInfo(
//       taskMoreInfo.info,
//     ))) {
//       infos.splice(0, infos.length)
//       infos.push(
//         ...parseInfo(
//           taskMoreInfo.info,
//         ),
//       )
//       if (hot) {
//         ElMessage.success('表单信息有更新')
//       }
//     }
//     // refreshWaitTime(false)
//     isLoadingData.value = false
//   })
// }

// const handleBlur = () => {
//   readyRefresh.value = true
// }
// const handleFocus = () => {
//   if (readyRefresh.value && !disableForm.value) {
//     readyRefresh.value = false
//     refreshTaskMoreInfo(true)
//   }
// }

onMounted(async () => {
  isLoadingData.value = true;
  sid.value = $route.params.sid as string;

  defaultTaskKey.value = await (await TaskApi.getDefaultTask()).data.key;
  console.log("get default task key:", defaultTaskKey.value);

  TaskApi.getTaskInfo(defaultTaskKey.value).then(
    (res) => {
      Object.assign(
        taskInfo,
        res.data, 1
      )
    },
  ).catch((err) => {
    if (err.code === 4001) {
      ElMessage.error('任务不存在')
      defaultTaskKey.value = ''
      taskInfo.name = '任务不存在'
    }
  })
  // refreshTaskMoreInfo()
  // refreshWaitTime()

  // 卡控人员限制
  if (taskMoreInfo.people) {
    const name = await confirmPeopleName()
    if (!name) {
      ElMessage.warning(
        '请填写有效的姓名',
      )
      return
    }
    const {
      data: { exist },
    } = await PeopleApi.checkPeopleIsExist(
      defaultTaskKey.value,
      name,
    )
    if (!exist) {
      ElMessage.warning(
        '你不在此次提交名单中,如有疑问请联系管理员',
      )
      return
    }
    peopleName.value = name
  }

  // console.log("infos", infos);

  const { data: { isSubmit } } = await FileApi.checkStudentSubmitStatus(
    defaultTaskKey.value,
    parseInt(sid.value),
    peopleName.value
  );

  // 已经提交的话就直接展示，没有提交的话跳转到展示页面

  if (isSubmit) {
    const submittedFile = await FileApi.getStudentSubmitFile(defaultTaskKey.value, parseInt(sid.value), peopleName.value)
    displayData.image.link = submittedFile.data.link;
    displayData.image.mimeType = submittedFile.data.mimeType;
    console.log(displayData.image.link);
  }

  isLoadingData.value = false;

  // // 页面隐藏
  // window.addEventListener('blur', handleBlur)

  // // 页面展示
  // window.addEventListener('focus', handleFocus)
})

onUnmounted(() => {
  // // 页面隐藏
  // window.removeEventListener('blur', handleBlur)
  // // 页面展示
  // window.removeEventListener('focus', handleFocus)
})
</script>
<style scoped lang="scss">
.task-panel :deep(ul.el-upload-list) {
  border: 1px dashed #d4d4d4;
  padding: 10px;

  &::before {
    content: '此处展示选择文件列表';
    font-size: 12px;
    position: relative;
    bottom: 4px;
  }
}

.task-panel :deep(.el-upload-list__item-name) {
  display: block;
  overflow: hidden;
  max-width: 290px;
  text-overflow: ellipsis;
  word-break: keep-all;
}

.task-panel :deep(.is-ready .el-icon--close) {
  display: block;
  color: black;
}

.task-panel {
  background-color: #f3f6f8;
  padding-bottom: 1rem;
}

.pc-nav {
  background-color: #fff;
  display: flex;
  padding: 10px;
  justify-content: space-between;
  align-items: center;

  .exit {
    cursor: pointer;
  }

  .nav {
    display: flex;

    nav {
      display: flex;
      align-items: center;

      .nav-item {
        font-size: 1rem;
        color: #595959;
        padding: 10px;
        cursor: pointer;

        &.active {
          color: #409eff !important;
          font-weight: 600;
        }
      }
    }

    .exit {
      color: #595959;
    }
  }

  .logo {
    width: 180px;
    margin: 0 10px;

    img {
      height: 40px;
    }
  }
}

.panel {
  max-width: 1024px;
  padding: 1em;
  background-color: #fff;
  margin: 10px auto;
  box-sizing: border-box;
  box-shadow: 0 2px 12px 0 rgb(0 0 0 / 10%);
  border-radius: 4px;

  .name {
    text-align: center;
  }

  .ddl {
    margin-top: 10px;
    color: #919191;
    font-size: 14px;
  }

  .infos {
    max-width: 400px;
    margin: auto;

    >div {
      margin-bottom: 10px;
    }
  }
}

.withdraw {
  text-align: right;
}

.tip-wrapper {
  line-height: 20px;
  text-align: left;
  word-break: break-all;
  max-height: 100px;
  overflow: hidden;
  padding: 0 20px;
  color: #E6A23C;
  max-width: 320px;
  font-size: 14px;
}
</style>
