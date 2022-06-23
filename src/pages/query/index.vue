<template>
  <div class="task-panel">
    <div class="pc-nav">
      <div class="nav">
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
      studentId
    ">
      <div>
        <el-divider>提交信息</el-divider>
        <div class="infos">
          <div>
            <span>学号：</span><span>{{ displayData.relativeData.studentId }}</span>
          </div>
          <div>
            <span>姓名：</span><span>{{ displayData.relativeData.name }}</span>
          </div>
        </div>
      </div>

      <img v-bind:src="displayData.image.link" v-if="displayData.image.link"
        style="max-width: 100%; max-height: 700px;" />
    </div>
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
  TaskApi,
} from '@/apis'

const $store = useStore()
const isMobile = computed(() => $store.getters['public/isMobile'])
// 顶部导航
const $router = useRouter()
const $route = useRoute()

const defaultTaskKey = ref(null)

const pcNavs = reactive([
  {
    title: '我也要提交',
    path: '/',
  },
])
const handleNav = (idx: number) => {
  $router.push({
    path: defaultTaskKey.value ? `/task/${defaultTaskKey.value}` : pcNavs[idx].path,
  })
}

interface StudentData {
  studentId: number,
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
const studentId = ref('')

const displayData = reactive<DisplayData>({
  relativeData: {
    studentId: 0,
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

const isEqualInfos = (a: InfoItem[] = [], b: InfoItem[] = []) => {
  if (a.length !== b.length) {
    return false
  }
  return a.every((v, i) => (v.type === b[i].type && v.text === b[i].text && isEqualInfos(v.children, b[i].children)))
}

onMounted(async () => {
  isLoadingData.value = true;
  studentId.value = $route.params.sutdentId as string;

  displayData.relativeData.studentId = parseInt(studentId.value);

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

  const { data: { isSubmit } } = await FileApi.checkStudentSubmitStatus(
    defaultTaskKey.value,
    displayData.relativeData.studentId,
    peopleName.value
  );

  // 已经提交的话就直接展示，没有提交的话跳转到展示页面
  if (isSubmit) {
    const submittedFile = await FileApi.getStudentSubmitFile(
      defaultTaskKey.value,
      displayData.relativeData.studentId,
      peopleName.value
    )
    displayData.image.link = submittedFile.data.link;
    displayData.image.mimeType = submittedFile.data.mimeType;
    const { data: { info } } = submittedFile;
    console.log(info);
    const moreInfo = JSON.parse(info);
    moreInfo.forEach(e => {
      if (e.text == '姓名') {
        displayData.relativeData.name = e.value;
      }
    });
  } else {
    $router.push({
      path: `/task/${defaultTaskKey.value}`
    })
  }

  isLoadingData.value = false;
})

onUnmounted(() => {
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
