<template>
  <v-app style="min-height: '920px'">
    <v-main id="main">
      <v-snackbar dark color="orange" v-model="snackbar" :timeout="timeout">
        {{ reportText }}
        <template v-slot:action="{ attrs }">
          <v-btn text v-bind="attrs" @click="snackbar = false">
            <v-icon>
              mdi-close
            </v-icon>
          </v-btn>
        </template>
      </v-snackbar>
      <v-container fluid class="fill-height">
        <v-row class="align-center justify-center" style="height: 100%">
          <v-col cols="6" style="height: 100%" class="pl-6">
            <div class="pb-3" style="height: 55%">
              <v-card class="elevation-6 pa-0 overflow-y-auto" style="height: 100%">
                <p class="mb-1 headline">
                  参数说明
                </p>
                <v-card>
                  <v-tabs v-model="tab" background-color="primary" dark show-arrows>
                    <v-tab v-for="item in items" :key="item.tab">
                      {{ item.tab }}
                    </v-tab>
                  </v-tabs>
                  <v-tabs-items v-model="tab">
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">计算单词文本中可以构成多少个单词链（能够构成所有单词链的数目）</v-card-title>
                        <v-card-text>
                          <p>
                          -n参数统计该单词文本中共有多少条单词链，包含嵌套单词链
                          </p>
                          <p>
                            参数-n不要求和其他参数联合使用
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">计算最多单词数量的单词链</v-card-title>
                        <v-card-text>
                          <p>
                            -w参数加文件名的形式计算最多单词数量的英语单词链
                          </p>
                          <p>
                            需要保证单词的输出顺序满足其单词链顺序，即首尾相连
                          </p>
                          <p>
                            假如可能有多组最长的相连英语单词串，选取其中任意一组作为结果即可
                          </p>
                          <p>
                            参数-w与-n -c 等功能性参数不兼容
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">计算字母最多的单词链</v-card-title>
                        <v-card-text>
                          <p>
                          -c参数计算字母最多的英语单词链
                          </p>
                          <p>
                            需要保证单词的输出顺序满足其单词链顺序，即首尾相连
                          </p>
                          <p>
                            假如可能有多组字母数最多的单词链，选取其中任意一组作为结果即可
                          </p>
                          <p>
                            参数-c与-n -w这一类功能型参数不兼容，同时出现属于异常
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">指定单词链开头字母</v-card-title>
                        <v-card-text>
                          <p>
                          -c参数指定单词链的首字母
                          </p>
                          <p>
                            参数-h属于附加型参数，单独出现属于异常
                          </p>
                          <p>
                            假参数-h与-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">指定单词链开头字母</v-card-title>
                        <v-card-text>
                          <p>
                          -h参数指定单词链的首字母
                          </p>
                          <p>
                            参数-h属于附加型参数，单独出现属于异常
                          </p>
                          <p>
                            参数-h与-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">指定单词链结尾字母</v-card-title>
                        <v-card-text>
                          <p>
                            -t参数指定单词链的尾字母
                          </p>
                          <p>
                            参数-t属于附加型参数，单独出现属于异常
                          </p>
                          <p>
                            参数-h与-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">指定单词链中所有单词均不允许出现的首字母</v-card-title>
                        <v-card-text>
                          <p>
                            -j参数指定不允许出现的首字母
                          </p>
                          <p>
                            参数-j属于附加型参数，单独出现属于异常
                          </p>
                          <p>
                            参数-j与参数-h -t兼容，若-h与-j指定字母一致则以“不存在符合条件的单词链”处理
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                    <v-tab-item >
                      <v-card flat>
                        <v-card-title class="headline">允许单词文本隐含单词环</v-card-title>
                        <v-card-text>
                          <p>
                            -r参数表示允许单词文本隐含单词环
                          </p>
                          <p>
                            参数-r属于附加型参数，单独出现属于异常
                          </p>
                          <p>
                            参数-r与参数-h及-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                          </p>
                          <p>
                            特别指出，-r参数可能与-j参数复合使用，同时可能存在-h以及-t参数。这四个参数作为附加型参数，若出现逻辑上的冲突，以“不存在符合条件的单词链”处理。
                          </p>
                        </v-card-text>
                      </v-card>
                    </v-tab-item>
                  </v-tabs-items>
                </v-card>
              </v-card>
            </div>
            <div class="pb-0" style="height: 45%">
              <v-card class="elevation-6 overflow-y-auto" style="height: 100%">
                <v-container fluid class="fill-height py-0">
                  <v-row class="align-center justify-center" style="height: 100%">
                    <v-col class="pa-0" cols="4" style="height: 100%">
                      <v-list dark class="rounded-l pa-0 primary" style="height: 100%">
                        <v-list-item-group mandatory active-class="indicator" v-model="selectedMode" style="height: 100%">
                          <v-list-item v-for="(item, i) in modes" :key="i" style="height: 33%">
                            <v-list-item-content>
                              <v-list-item-title class="text-center">
                                {{ item }}
                              </v-list-item-title>
                            </v-list-item-content>
                          </v-list-item>
                        </v-list-item-group>
                      </v-list>
                    </v-col>
                    <v-col cols="8" style="height: 100%; display: flex" class="justify-center">
                      <v-card-title v-if="noAvailableOptions" class="justify-center text--secondary">
                        无可用选项： <br />
                        -n参数不能和其他参数一起使用
                      </v-card-title>
                      <v-col v-if="!noAvailableOptions" style="height: 100%" class="mode-options py-0 px-2">

                        <v-text-field label="首字母限制 -h" class="my-0" v-model="head" :rules="[rules.singleLetter]" />
                        <v-text-field label="尾字母限制 -t" class="my-0" v-model="tail" :rules="[rules.singleLetter]" />
                        <v-text-field label="不允许出现的首字母 -j" class="my-0" v-model="reject" :rules="[rules.singleLetter]" />
                        <v-checkbox label="允许单词环 -r" class="mt-0" v-model="allowRing"></v-checkbox>

                      </v-col>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card>
            </div>

          </v-col>
          <v-col cols="6" style="height: 100%" class="pr-6">
            <div class="pb-3" style="height: 55%">
              <v-card class="elevation-6" style="height: 100%; display: flex; flex-direction: column">

                <v-toolbar class="elevation-0 py-1" style="flex-grow: 0">
                  <v-btn dark class="primary" @click="importText"> 导入文本文件 <v-icon right light> mdi-file-import </v-icon>
                  </v-btn>
                  <v-spacer />
                  <v-btn dark class="primary" @click="clearInputText"> 清空 <v-icon right light> mdi-cached </v-icon>
                  </v-btn>
                  <v-spacer />
                  <v-btn class="primary" @click="solve" :loading="calculating" :dark="!calculating"
                    :disabled="calculating">
                    求解
                    <v-icon right light> mdi-send </v-icon>
                  </v-btn>
                </v-toolbar>

                <v-card-text style="flex-grow: 1" class="pt-3">
                  <v-textarea filled no-resize height="86%" placeholder="在此处输入单词文本" style="height: 100%"
                    v-model="inputText" />
                </v-card-text>
              </v-card>
            </div>
            <div class="pb-0" style="height: 45%">
              <v-card class="elevation-6" style="height: 100%; display: flex; flex-direction: column">
                <v-toolbar class="elevation-0 py-1" style="flex-grow: 0">
                  <v-btn dark class="primary" @click="exportText"> 导出文本文件 <v-icon right light> mdi-file-export </v-icon>
                  </v-btn>
                  <v-spacer />
                  <v-btn dark class="primary" @click="clearOutputText"> 清空 <v-icon right light> mdi-cached </v-icon>
                  </v-btn>
                </v-toolbar>
                <v-card-text style="flex-grow: 1" class="pt-3">
                  <v-textarea filled no-resize height="93%" placeholder="求解结果" style="height: 100%" readonly
                    v-model="outputText" :success-messages="runMessage" />
                </v-card-text>
              </v-card>
            </div>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
const path = window.require('path')
const ffi = window.require('ffi-napi')
const corePtr = ffi.DynamicLibrary(path.resolve('./core.dll')).get('gui_engine')
const core = ffi.ForeignFunction(corePtr, 'string', ['string', 'int', 'char', 'char', 'bool'])
const moment = window.require('moment')

export default {
  name: 'App',
  data: () => ({
    headers: [
      {
        text: '功能',
        align: 'start',
        sortable: false,
        value: 'function',
      },
      { text: '参数', value: 'parameter' },
      { text: '详细描述', value: 'details' },

    ],
    desserts: [
      {
        function: '单词链数量',
        parameter: '-n',
        detail: '计算单词文本中可以构成多少个单词链（能够构成所有单词链的数目）;该参数不能和其他参数联合使用',
      },
      {
        function: '单词数最多',
        parameter: '-w',
        detail: '计算最多单词数量的单词链，与-n和-c等功能性参数不兼容',
      },
      {
        function: '字母数最多',
        parameter: '-c',
        detail: '计算字母最多的单词链，与-n和-w等功能性参数不兼容',
      },
      {
        function: '首字母限制',
        parameter: '-h',
        detail: '指定单词链的首字母，属于附加型参数，与-t兼容',
      },
      {
        function: '尾字母限制',
        parameter: '-t',
        detail: '指定单词链的尾字母，属于附加型参数，与-h兼容',
      },
      {
        function: '不允许出现的首字母',
        parameter: '-j',
        detail: '指定不允许出现的首字母，属于附加型参数，与-h和-t兼容',
      },
      {
        function: '允许单词环',
        parameter: '-r',
        detail: '允许单词文本隐含单词环，与-h,-t和-j兼容',
      },
    ],
    tab: null,
    items: [
      { tab: '-n', content: '单词链数量\n-n' },
      { tab: '-w', content: '<p class="mb-0">Etiam vitae tortor. Curabitur ullamcorper ultricies nisi. Sed magna purus, fermentum eu, tincidunt eu, varius ut, felis. Aliquam lobortis. Suspendisse potenti.</p>' },
      { tab: '-c', content: 'Tab 3 Content' },
      { tab: '-h', content: 'Tab 4 Content' },
      { tab: '-t', content: 'Tab 5 Content' },
      { tab: '-j', content: 'Tab 6 Content' },
      { tab: '-r', content: 'Tab 7 Content' },
    ],
    modes: ['单词链数量 -n', '单词数最多 -w', '字母数最多 -c'],
    selectedMode: 1,
    head: '',
    tail: '',
    reject: '',
    allowRing: false,
    inputText: '',
    outputText: '',
    calculating: false,
    rules: {
      singleLetter: v => !v || /^[a-zA-Z]$/.test(v) || '只能指定单个英文字母',
    },
    snackbar: false,
    timeout: 3000,
    reportText: '',
    runMessage: ''
  }),
  computed: {
    noAvailableOptions: function () {
      return this.selectedMode === 0
    }
  },
  methods: {
    reportError(msg) {
      this.reportText = msg
      this.snackbar = true
    },
    clearInputText() {
      this.inputText = ''
    },
    clearOutputText() {
      this.outputText = ''
    },
    importText() {
      const { dialog } = require('@electron/remote')
      const fs = require('fs')
      const path = require('path')
      dialog.showOpenDialog({
        title: '选择导入文件',
        buttonLabel: '导入',
        filters: [{ name: '文本文件', extensions: ['txt'] }],
        properties: [
          'openFile',
          'showHiddenFiles'
        ]
      }).then(e => {
        if (e.canceled === false) {
          fs.readFile(
            path.resolve(e.filePaths[0]),
            'utf-8',
            (err, data) => {
              if (err) this.reportError(err.message);
              this.inputText = data.toString();
            }
          )
        }
      })
    },
    exportText() {
      const { dialog } = require('@electron/remote')
      const fs = require('fs')
      const path = require('path')
      dialog.showOpenDialog({
        title: '指定导出文件',
        buttonLabel: '保存',
        filters: [{ name: '文本文件', extensions: ['txt'] }],
        properties: [
          'openFile',
          'showHiddenFiles',
          'promptToCreate'
        ]
      }).then(e => {
        if (e.canceled === false) {
          fs.writeFile(
            path.resolve(e.filePaths[0]),
            this.outputText,
            'utf-8',
            (err) => {
              if (err) this.reportError(err.message)
            }
          )
        }
      })
    },
    solve() {
      this.calculating = true
      this.outputText = ''
      this.runMessage = ''
      let start = moment()
      core.async(
        this.inputText,
        [0, this.allowRing ? 3 : 1, 2, this.allowRing ? 3 : 1][this.selectedMode],
        this.noAvailableOptions || !this.head ? 0 : this.head.charCodeAt(0),
        this.noAvailableOptions || !this.tail ? 0 : this.tail.charCodeAt(0),
        this.noAvailableOptions || !this.reject ? 0 : this.reject.charCodeAt(0),
        this.selectedMode === 3,
        (e, d) => {
          if (e) this.reportError(e)
          if (/^WordList-GUI: /.test(d)) {
            this.reportError(d.substring(14))
          } else {
            this.outputText = d
            this.runMessage = '计算用时：' + moment().diff(start) + 'ms'
          }
          this.calculating = false
        }
      )
    },
  }
};
</script>

<style lang="css">
.v-textarea>* {
  height: 100% !important;
}

/*textarea {
  -ms-overflow-style: none;
}
textarea::-webkit-scrollbar {
  display: none;
}*/
.mode-options {
  display: grid !important;
  grid-template-rows: repeat(3, minmax(0, 1fr));
  margin: 0 !important;
}

html {
  overflow: hidden !important;
}

#main:before {
  content: "";
  background: url('assets/ironman-draw.png') no-repeat center center fixed !important;
  background-size: cover !important;
  opacity: 0.3;
  position: absolute;
  top: 0;
  right: 0;
  left: 0;
  bottom: 0;
}
</style>
