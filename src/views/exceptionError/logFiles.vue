<template>
  <div class="app-container">
    <el-row>
      <el-col :span="24">
        <el-table
          v-loading="loading"
          :data="files"
          stripe
          style="width: 100%"
        >
          <el-table-column prop="name" :label="$t('file.name')" />
          <el-table-column prop="size" :label="$t('file.size')" />
          <el-table-column prop="lastModified" :label="$t('file.lastModified')" />
          <el-table-column>
            <template slot-scope="scope">
              <el-button type="primary" icon="el-icon-search" @click="getFile(scope.row.name)">
                {{ $t('common.details') }}
              </el-button>
            </template>
          </el-table-column>
          <el-table-column align="right">
            <template slot="header">
              <el-button type="primary" icon="el-icon-refresh" @click="getFiles" />
            </template>
          </el-table-column>
        </el-table>
      </el-col>
    </el-row>
    <el-drawer
      :title="title"
      :with-header="false"
      :visible.sync="drawer"
      :direction="direction"
      size="80%"
      :before-close="handleClose"
    >
      <el-row>
        <el-divider content-position="left">{{ title }}</el-divider>
        <el-col :span="24" class="file-info" :style="{ height: offsetHeight + 'px'}">
          <pre>{{ file }}</pre>
        </el-col>
      </el-row>
    </el-drawer>
  </div>
</template>

<script>
import { files, file } from '@/api/exception'

export default {
  name: 'LogFiles',
  data() {
    return {
      files: [],
      file: '',
      title: '',
      loading: true,
      drawer: false,
      direction: 'rtl',
      offsetHeight: 0
    }
  },
  mounted() {
    this.getFiles()
  },
  methods: {
    getFiles() {
      this.loading = true
      files({}).then(response => {
        const { files } = response.data
        this.files = files
        this.loading = false
      })
    },
    getFile(fileName) {
      const loading = this.$loading({
        lock: true,
        text: 'Loading',
        spinner: 'el-icon-loading',
        background: 'rgba(0, 0, 0, 0.7)'
      })
      file({
        file: fileName
      }).then(response => {
        loading.close()
        const { file } = response.data
        this.title = fileName
        this.file = file
        this.drawer = true
        this.offsetHeight = document.body.offsetHeight - 50
      }).catch(reason => {
        loading.close()
      })
    },
    handleClose(done) {
      this.title = ''
      this.file = ''
      done()
    }
  }
}
</script>

<style scoped>
.file-info{
  padding: 0 15px 15px 15px;
  font-size: 14px;
  font-family: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;
  overflow-x: auto;
  overflow-y: auto;
}
</style>
