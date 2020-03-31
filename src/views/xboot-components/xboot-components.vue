<template>
  <div>
    <Card>
      <Layout>
        <Sider hide-trigger style="background: #fff;max-width: 220px;flex: 0 0 220px;">
          <Menu
            active-name="1-0"
            theme="light"
            width="auto"
            :open-names="['1','2','3']"
            @on-select="currName=$event"
          >
            <Submenu name="1">
              <template slot="title">
                <Icon type="md-ionic" />XBoot通用组件
              </template>
              <MenuItem name="1-0">全局Loading加载动画</MenuItem>
              <MenuItem name="0-0">
                <Badge dot :offset="[5,-3]">树表格组件</Badge>
              </MenuItem>
              <MenuItem name="1-1">倒计时按钮</MenuItem>
              <MenuItem name="1-2">图标选择输入框</MenuItem>
              <MenuItem name="1-3">部门级联选择</MenuItem>
              <MenuItem name="1-4">部门树选择</MenuItem>
              <MenuItem name="1-5">用户抽屉选择</MenuItem>
              <MenuItem name="1-6">图片上传输入框</MenuItem>
              <MenuItem name="1-7">图片上传缩略图</MenuItem>
              <MenuItem name="1-8">身份验证全屏弹框</MenuItem>
              <MenuItem name="1-9">密码强度输入框</MenuItem>
            </Submenu>
            <Submenu name="3">
              <template slot="title">
                <Icon type="ios-create" />
                <Badge dot :offset="[5,-3]">富文本编辑器(付费)</Badge>
              </template>
              <MenuItem name="3-1">wangEditor</MenuItem>
              <MenuItem name="3-2">Quill</MenuItem>
            </Submenu>
            <Submenu name="2">
              <template slot="title">
                <Icon type="md-git-compare" />工作流组件(付费)
              </template>
              <MenuItem name="2-1">工作流程选择发起</MenuItem>
              <MenuItem name="2-2">通过流程key直接发起</MenuItem>
              <MenuItem name="2-3">取消撤回申请</MenuItem>
            </Submenu>
          </Menu>
        </Sider>
        <Content :style="{padding: '0 24px 24px 24px', minHeight: '280px', background: '#fff'}">
          <div v-show="currName=='0-0'">
            <tree-table />
          </div>
          <div v-show="currName=='1-0'">
            <Alert type="warning" show-icon>说明：大部分组件为包含真实数据接口的简单封装，方便大家的直接复用！</Alert>
            <Divider class="blue" orientation="left">全局Loading加载动画</Divider>
            <Button @click="showLoading">显示右上角加载动画</Button>
            <Button @click="closeLoading" style="margin-left:5px">关闭右上角加载动画</Button>
            <h3 class="article">使用方式</h3>修改全局Vuex即可，开启：
            <code>this.$store.commit("setLoading", true)</code> 关闭：
            <code>this.$store.commit("setLoading", false)</code>
            <Divider class="blue" orientation="left">iView官方 LoadingBar加载进度条</Divider>
            <Button @click="start">开始加载</Button>
            <Button @click="finish" style="margin-left:5px">结束加载</Button>
            <Button @click="error" style="margin-left:5px">错误</Button>
            <h3 class="article">说明</h3>如果你觉得上方XBoot提供的动画不明显，你还可以使用iView官方自带的顶部
            <a
              href="https://www.iviewui.com/components/loading-bar"
              target="_blank"
            >LoadingBar组件</a>
          </div>
          <div v-show="currName=='1-1'">
            <Divider class="blue" orientation="left">倒计时按钮</Divider>
            <count-down-button countTime="10" />
            <h3 class="article">提示</h3>你可以将
            <code>autoCountDown</code> 属性设置为
            <code>false</code>，即可手动调用开始倒计时方法
            <code>startCountDown()</code>，用于验证手机号或发送短信成功后开始倒计时等场景。
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data20" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data22" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data21" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-2'">
            <Divider class="blue" orientation="left">图标选择输入框</Divider>
            <icon-choose v-model="icon" style="width:400px"></icon-choose>
            <h3 class="article">基础用法</h3>基本用法，使用
            <code>v-model</code> 实现数据的双向绑定。
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data2" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data1" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-3'">
            <Divider class="blue" orientation="left">部门级联选择</Divider>
            <department-choose style="width:300px" @on-change="handleSelectDep" ref="dep"></department-choose>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data3" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data4" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-4'">
            <Divider class="blue" orientation="left">部门树选择</Divider>
            <department-tree-choose
              multiple
              style="width:400px"
              @on-change="handleSelectDepTree"
              ref="depTree"
            ></department-tree-choose>
            <div style="margin-top:10px;">{{selectDeps}}</div>
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data5" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data6" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data7" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-5'">
            <Divider class="blue" orientation="left">用户抽屉选择</Divider>
            <user-choose text="点我选择用户" @on-change="handleSelectUser" ref="user"></user-choose>
            <div style="margin-top:10px;">{{selectUsers}}</div>
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data8" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data9" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data10" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-6'">
            <Divider class="blue" orientation="left">图片上传文本框</Divider>
            <upload-pic-input v-model="picUrl" style="width:400px" ref="upload"></upload-pic-input>
            <h3 class="article">基础用法</h3>基本用法，使用
            <code>v-model</code> 实现数据的双向绑定。
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data11" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data12" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-7'">
            <Divider class="blue" orientation="left">图片上传缩略图</Divider>
            <upload-pic-thumb @on-change="picUrls=$event" ref="uploadThumb"></upload-pic-thumb>
            {{picUrls}}
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data23" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data24" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data25" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-8'">
            <Divider class="blue" orientation="left">身份验证全屏弹框</Divider>
            <Button @click="showCheckPass">开始验证</Button>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data26" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data27" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='1-9'">
            <Divider class="blue" orientation="left">密码强度输入框</Divider>
            <setPassword v-model="password" style="width:300px" />
            <h3 class="article">基础用法</h3>基本用法，使用
            <code>v-model</code> 实现数据的双向绑定。
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data28" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data29" border size="small" width="1000"></Table>
          </div>

          <div v-show="currName=='3-1'">
            <Alert type="info" show-icon>
              基于
              <a href="http://www.wangeditor.com" target="_blank">wangEditor</a> 封装，已配置好图片上传(上传至XBoot文件服务或Base64)、表情包。扩展编辑HTML代码、全屏预览、清空、XSS攻击过滤等。
            </Alert>
            <editor id="editor" v-model="editorData"></editor>
            <h3 class="article">基础用法</h3>使用
            <code>v-model</code> 实现数据的双向绑定。单页面同时使用两个及以上该组件时，需设定不同的id值加以区分。已有新浪表情包扩展配置：
            <code>src/libs/emoji.js</code>。
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data30" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data31" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data32" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='3-2'">
            <Alert type="info" show-icon>
              基于
              <a href="https://github.com/quilljs/quill" target="_blank">quill</a> 封装。已配置好图片上传(上传至XBoot文件服务或Base64)。扩展编辑HTML代码、全屏预览、清空、XSS攻击过滤等。
            </Alert>
            <quill id="quill" v-model="quillData"></quill>
            <h3 class="article">基础用法</h3>使用
            <code>v-model</code> 实现数据的双向绑定。单页面同时使用两个及以上该组件时，需设定不同的id值加以区分。
            <h3 class="article">props</h3>
            <Table :columns="props" :data="data33" border size="small" width="1000"></Table>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data34" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data35" border size="small" width="1000"></Table>
          </div>

          <div v-show="currName=='2-1'">
            <Alert
              type="warning"
              show-icon
            >流程审批通用状态及结果保存至ActBusiness表中，添加业务时记得向该表中关联表ID等数据，参考开发文档工作流部分</Alert>
            <Divider class="blue" orientation="left">工作流程选择发起</Divider>
            <Button @click="processModalVisible=true">发起流程</Button>
            <Alert
              show-icon
              style="width:700px;margin-top:15px;"
            >请务必在显示组件前调用setID方法传入ActBusinessId数据！</Alert>
            <div style="font-size:12px;margin-bottom:10px">示例（仅为演示传入ID为测试数据123456，所以会报错）：</div>
            <Button @click="processChoose">发起流程</Button>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data14" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data15" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='2-2'">
            <Divider class="blue" orientation="left">通过流程key直接发起</Divider>
            <div
              style="font-size:12px;margin-bottom:10px"
            >示例（仅为演示传入流程key为请假申请leave，传入ID为测试数据123456，所以会报错）：</div>
            <Button @click="startByKey" :loading="processLoading">发起流程</Button>
            <h3 class="article">events</h3>
            <Table :columns="events" :data="data16" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data17" border size="small" width="1000"></Table>
          </div>
          <div v-show="currName=='2-3'">
            <Divider class="blue" orientation="left">取消撤回申请</Divider>
            <div
              style="font-size:12px;margin-bottom:10px"
            >示例（仅为演示传入ActBusiness表ID和流程实例的procInstId均为测试数据123456，所以会报错）：</div>
            <Button @click="cancelProcess">撤回申请</Button>

            <h3 class="article">events</h3>
            <Table :columns="events" :data="data18" border size="small" width="1000"></Table>
            <h3 class="article">methods</h3>
            <Table :columns="methods" :data="data19" border size="small" width="1000"></Table>
          </div>
        </Content>
      </Layout>
    </Card>

    <check-password ref="checkPass" @on-success="checkSuccess" />

    <Drawer title="选择流程" closable v-model="processModalVisible" :width="800" draggable>
      <process-choose ref="processChoose" />
    </Drawer>

    <process-start
      ref="processStart"
      @on-loading="processLoading=true"
      @on-loaded="processLoading=false"
    />

    <process-cancel ref="processCancel" />
  </div>
</template>

<script>
import TreeTable from "./tree-table";
import iconChoose from "@/views/my-components/xboot/icon-choose";
import countDownButton from "@/views/my-components/xboot/count-down-button";
import departmentChoose from "@/views/my-components/xboot/department-choose";
import departmentTreeChoose from "@/views/my-components/xboot/department-tree-choose";
import userChoose from "@/views/my-components/xboot/user-choose";
import uploadPicInput from "@/views/my-components/xboot/upload-pic-input";
import uploadPicThumb from "@/views/my-components/xboot/upload-pic-thumb";
import checkPassword from "@/views/my-components/xboot/check-password";
import setPassword from "@/views/my-components/xboot/set-password";
import editor from "@/views/my-components/xboot/editor";
import quill from "@/views/my-components/xboot/quill";
import processChoose from "@/views/my-components/xboot/process-choose";
import processStart from "@/views/my-components/xboot/process-start";
import processCancel from "@/views/my-components/xboot/process-cancel";
export default {
  name: "xboot-components",
  components: {
    TreeTable,
    iconChoose,
    countDownButton,
    departmentChoose,
    userChoose,
    departmentTreeChoose,
    uploadPicInput,
    uploadPicThumb,
    checkPassword,
    setPassword,
    editor,
    quill,
    processChoose,
    processStart,
    processCancel
  },
  data() {
    return {
      password: "",
      currName: "1-0",
      processModalVisible: false,
      icon: "",
      selectDeps: [],
      selectUsers: [],
      picUrl: "",
      picUrls: [],
      checkPass: false,
      processLoading: false,
      editorData:
        '<img src="http://img.t.sinajs.cn/t4/appstyle/expression/ext/normal/a1/2018new_doge02_org.png" alt="[doge]">',
      quillData:
        '<img src="http://img.t.sinajs.cn/t4/appstyle/expression/ext/normal/a1/2018new_doge02_org.png" alt="[doge]">',
      events: [
        {
          title: "事件名",
          key: "name",
          width: 150
        },
        {
          title: "说明",
          key: "type",
          width: 300
        },
        {
          title: "返回值",
          key: "value"
        }
      ],
      props: [
        {
          title: "属性",
          key: "name",
          width: 130
        },
        {
          title: "说明",
          key: "desc"
        },
        {
          title: "类型",
          key: "type",
          width: 130
        },
        {
          title: "默认值",
          key: "value"
        }
      ],
      methods: [
        {
          title: "方法名",
          key: "name",
          width: 150
        },
        {
          title: "说明",
          key: "type",
          width: 300
        },
        {
          title: "参数",
          key: "value"
        }
      ],
      data1: [
        {
          name: "on-change",
          type: "返回用户选择的图标名或用户输入的图标文本",
          value: "value（输入框文本值）"
        }
      ],
      data2: [
        {
          name: "value",
          desc: "绑定的值，可使用 v-model 双向绑定",
          type: "String",
          value: "空"
        },
        {
          name: "size",
          desc: "输入框尺寸，可选值为large、small、default或者不设置",
          type: "String",
          value: "-"
        },
        {
          name: "placeholder",
          desc: "占位文本",
          type: "String",
          value: "输入图标名或选择图标"
        },
        {
          name: "disabled",
          desc: "设置输入框和选择按钮为禁用状态",
          type: "Boolean",
          value: "false"
        },
        {
          name: "readonly",
          desc: "设置输入框为只读",
          type: "Boolean",
          value: "false"
        },
        {
          name: "maxlength",
          desc: "设置输入框最大输入长度",
          type: "Number",
          value: "-"
        },
        {
          name: "icon",
          desc: "设置上传按钮图标",
          type: "String",
          value: "md-ionic"
        }
      ],
      data3: [
        {
          name: "on-change",
          type: "返回点击部门ID",
          value: "value（点击部门ID）"
        }
      ],
      data4: [
        {
          name: "clearSelect",
          type: "清空已选数据",
          value: "无"
        }
      ],
      data5: [
        {
          name: "placeholder",
          desc: "提示文字",
          type: "String",
          value: "点击选择部门"
        },
        {
          name: "multiple",
          desc: "是否选开启多选，默认false不开启",
          type: "Boolean",
          value: "false"
        },
        {
          name: "clearable",
          desc: "是否显示清空按钮",
          type: "Boolean",
          value: "true"
        }
      ],
      data6: [
        {
          name: "on-change",
          type: "返回选择部门id字符串(非多选)或数组(开启多选)",
          value:
            '选择部门id字符串或数组Array，仅包含部门id，例如"1"(非多选)或["1","2","3"](开启多选)'
        },
        {
          name: "on-clear",
          type: "开启clearable时可用，点击清空按钮时触发",
          value: "无"
        }
      ],
      data7: [
        {
          name: "setData",
          type: "设置已选部门数据（回显使用）",
          value:
            "第一个参数为部门id字符串(非多选)或数组Array(开启多选)，第二个参数为部门标题（String）"
        }
      ],
      data8: [
        {
          name: "text",
          desc: "选择用户按钮文字",
          type: "String",
          value: "选择用户"
        },
        {
          name: "icon",
          desc: "选择用户按钮图标",
          type: "String",
          value: "md-person-add"
        },
        {
          name: "all",
          desc: "是否选择所有用户",
          type: "Boolean",
          value: "false"
        }
      ],
      data9: [
        {
          name: "on-change",
          type: "返回选择用户数组",
          value:
            '选择用户数组Array，包含用户id和username，例如 [{"id":"1","username":"name"}]'
        }
      ],
      data10: [
        {
          name: "setData",
          type: "设置已选用户数据（回显使用）",
          value:
            '用户数组，需包含用户id和username，例如 [{"id":"1","username":"name"}]'
        }
      ],
      data11: [
        {
          name: "value",
          desc: "绑定的值，可使用 v-model 双向绑定",
          type: "String",
          value: "空"
        },
        {
          name: "size",
          desc: "输入框尺寸，可选值为large、small、default或者不设置",
          type: "String",
          value: "-"
        },
        {
          name: "placeholder",
          desc: "占位文本",
          type: "String",
          value: "可输入图片链接"
        },
        {
          name: "disabled",
          desc: "设置输入框和上传按钮为禁用状态",
          type: "Boolean",
          value: "false"
        },
        {
          name: "readonly",
          desc: "设置输入框为只读",
          type: "Boolean",
          value: "false"
        },
        {
          name: "maxlength",
          desc: "设置输入框最大输入长度",
          type: "Number",
          value: "-"
        },
        {
          name: "icon",
          desc: "设置上传按钮图标",
          type: "String",
          value: "ios-cloud-upload-outline"
        }
      ],
      data12: [
        {
          name: "on-change",
          type: "返回完整上传图片路径或用户输入的链接",
          value: "value（输入框文本值）"
        }
      ],
      data14: [
        {
          name: "on-submit",
          type: "Boolean",
          value: "仅成功提交申请触发返回true，用于刷新表单显示审批状态"
        }
      ],
      data15: [
        {
          name: "setID",
          type: "显示组件前务必调用改方法传入ActBusinessId数据",
          value: "ActBusiness表ID"
        }
      ],
      data16: [
        {
          name: "on-loading",
          type: "Boolean",
          value: "加载中状态，传入key后需加载流程信息，加载中触发返回true"
        },
        {
          name: "on-loaded",
          type: "Boolean",
          value: "加载完毕状态，传入key后需加载流程信息，加载完毕触发返回true"
        },
        {
          name: "on-submit",
          type: "Boolean",
          value: "仅成功提交申请触发返回true，用于刷新表单显示审批状态"
        }
      ],
      data17: [
        {
          name: "show",
          type: "显示组件，务必传入申请的流程标识key和ActBusinessId两个参数",
          value: "第一个参数为流程标识key，第二个参数为ActBusiness表ID"
        }
      ],
      data18: [
        {
          name: "on-submit",
          type: "Boolean",
          value: "仅成功提交申请触发返回true，用于刷新表单显示审批状态"
        }
      ],
      data19: [
        {
          name: "show",
          type: "显示组件，务必传入ActBusinessId和流程实例的procInstId两个参数",
          value: "第一个参数为ActBusiness表ID，第二个参数为流程实例的procInstId"
        }
      ],
      data20: [
        {
          name: "text",
          desc: "按钮默认文本",
          type: "String",
          value: "提交"
        },
        {
          name: "autoCountDown",
          desc: "点击后即自动开始倒计时，设置为false后可手动触发倒计时",
          type: "Boolean",
          value: "true"
        },
        {
          name: "countTime",
          desc: "倒计时时间，单位：秒",
          type: "Number",
          value: "60"
        },
        {
          name: "suffixText",
          desc: "倒计时中文本后缀，如'60秒后重试'，其中‘后重试’可自定义",
          type: "String",
          value: "后重试"
        },
        {
          name: "type",
          desc:
            "按钮类型，可选值为 default、primary、dashed、text、info、success、warning、error或者不设置",
          type: "String",
          value: "default"
        },
        {
          name: "ghost",
          desc: "幽灵属性，使按钮背景透明",
          type: "Boolean",
          value: "false"
        },
        {
          name: "size",
          desc: "按钮大小，可选值为large、small、default或者不设置",
          type: "String",
          value: "default"
        },
        {
          name: "shape",
          desc: "按钮形状，可选值为circle或者不设置",
          type: "String",
          value: "-"
        },
        {
          name: "long",
          desc: "开启后，按钮的长度为100%",
          type: "Boolean",
          value: "false"
        },
        {
          name: "disabled",
          desc: "设置按钮为禁用状态",
          type: "Boolean",
          value: "false"
        },
        {
          name: "loading",
          desc: "设置按钮为加载中状态",
          type: "Boolean",
          value: "false"
        },
        {
          name: "icon",
          desc: "设置按钮的图标类型",
          type: "String",
          value: "-"
        }
      ],
      data21: [
        {
          name: "startCountDown",
          type: "当autoCountDown设置为false时生效，手动开启倒计时",
          value: "无"
        }
      ],
      data22: [
        {
          name: "on-click",
          type: "用户点击事件",
          value: "无"
        }
      ],
      data23: [
        {
          name: "multiple",
          desc: "是否选开启多张上传，默认true开启，设为false仅限制一张",
          type: "Boolean",
          value: "true"
        },
        {
          name: "limit",
          desc:
            "限制上传数量，开启多张上传multiple设为true时生效，默认限制10张",
          type: "Number",
          value: "10"
        }
      ],
      data24: [
        {
          name: "on-change",
          type: "返回上传成功图片链接",
          value:
            "当开启多张上传时，返回图片链接数组，如['http://1.png','http://2.png']；限制单张时返回单个图片链接，如'http://3.png'"
        }
      ],
      data25: [
        {
          name: "setData",
          type: "设置图片链接值（回显使用）",
          value:
            "根据多张上传配置，传入多张图片链接数组或单张图片链接，如['http://1.png','http://2.png']或'http://3.png'"
        }
      ],
      data26: [
        {
          name: "on-success",
          type: "仅当验证密码正确触发回调",
          value: "true"
        }
      ],
      data27: [
        {
          name: "show",
          type: "显示密码验证组件",
          value: "无"
        }
      ],
      data28: [
        {
          name: "value",
          desc: "绑定的值，可使用 v-model 双向绑定",
          type: "String",
          value: "空"
        },
        {
          name: "size",
          desc: "输入框尺寸，可选值为large、small、default或者不设置",
          type: "String",
          value: "-"
        },
        {
          name: "placeholder",
          desc: "占位文本",
          type: "String",
          value: "请输入密码，长度为6-20个字符"
        },
        {
          name: "disabled",
          desc: "设置输入框和上传按钮为禁用状态",
          type: "Boolean",
          value: "false"
        },
        {
          name: "readonly",
          desc: "设置输入框为只读",
          type: "Boolean",
          value: "false"
        },
        {
          name: "maxlength",
          desc: "设置输入框最大输入长度，默认20",
          type: "Number",
          value: "20"
        }
      ],
      data29: [
        {
          name: "on-change",
          type:
            "强度等级得分0-5，包含数字、小写字母、大写字母、特殊字符、长度≥10各累加1分，≤1分强度为弱、2-4分强度为中、5分强度为强",
          value:
            "第一个参数为返回用户输入的内容，第二个参数为强度等级得分0-5，第三个参数为强度汉字"
        }
      ],
      data30: [
        {
          name: "value",
          desc: "绑定的值，可使用 v-model 双向绑定",
          type: "String",
          value: "空"
        },
        {
          name: "id",
          desc:
            "富文本的id值，用于绑定富文本编辑器，当同时使用两个及以上该组件时，需设定不同的id值加以区分",
          type: "String",
          value: "editor"
        },
        {
          name: "base64",
          desc:
            "是否使用base64保存图片，默认false上传至XBoot配置的文件存储服务中，不推荐使用base64存储",
          type: "Boolean",
          value: "false"
        },
        {
          name: "showExpand",
          desc: "是否显示顶部最右侧扩展按钮（编辑Html代码、预览、清空）",
          type: "Boolean",
          value: "true"
        },
        {
          name: "openXss",
          desc:
            "是否打开XSS过滤，注意！开启后将默认过滤掉样式，请自行添加白名单",
          type: "Boolean",
          value: "false"
        }
      ],
      data31: [
        {
          name: "on-change",
          type: "返回富文本编辑器内容",
          value: "value（富文本编辑器内容）"
        }
      ],
      data32: [
        {
          name: "setData",
          type: "设置富文本编辑器内容",
          value: "你要传入的内容，示例 setData(data)"
        }
      ],
      data33: [
        {
          name: "value",
          desc: "绑定的值，可使用 v-model 双向绑定",
          type: "String",
          value: "空"
        },
        {
          name: "id",
          desc:
            "富文本的id值，用于绑定富文本编辑器，当同时使用两个及以上该组件时，需设定不同的id值加以区分",
          type: "String",
          value: "editor"
        },
        {
          name: "minHeight",
          desc:
            "富文本最低高度，默认300px",
          type: "String",
          value: "300px"
        },
        {
          name: "base64",
          desc:
            "是否使用base64保存图片，默认false上传至XBoot配置的文件存储服务中，不推荐使用base64存储",
          type: "Boolean",
          value: "false"
        },
        {
          name: "expandHtml",
          desc: "是否显示顶部扩展 编辑Html代码 按钮",
          type: "Boolean",
          value: "true"
        },
        {
          name: "expandPreview",
          desc: "是否显示顶部扩展 预览 按钮",
          type: "Boolean",
          value: "true"
        },
        {
          name: "expandClear",
          desc: "是否显示顶部扩展 清空 按钮",
          type: "Boolean",
          value: "true"
        },
        {
          name: "openXss",
          desc:
            "是否打开XSS过滤，注意！开启后将默认过滤掉样式，请自行添加白名单",
          type: "Boolean",
          value: "false"
        }
      ],
      data34: [
        {
          name: "on-change",
          type: "返回富文本编辑器内容",
          value: "value（富文本编辑器内容）"
        }
      ],
      data35: [
        {
          name: "setData",
          type: "设置富文本编辑器内容",
          value: "你要传入的内容，示例 setData(data)"
        }
      ]
    };
  },
  methods: {
    init() {},
    showLoading() {
      this.$store.commit("setLoading", true);
    },
    closeLoading() {
      this.$store.commit("setLoading", false);
    },
    start() {
      this.$Loading.start();
    },
    finish() {
      this.$Loading.finish();
    },
    error() {
      this.$Loading.error();
    },
    handleSelectDep(v) {
      this.$Message.info(`所选部门ID为 ${v}`);
    },
    handleSelectUser(v) {
      this.selectUsers = v;
    },
    selectAllUser() {
      this.$refs.user.setSelectAllUser();
    },
    handleSelectDepTree(v) {
      this.selectDeps = v;
    },
    processChoose() {
      this.$refs.processChoose.setID("123456");
      this.processModalVisible = true;
    },
    startByKey() {
      this.$refs.processStart.show("leave", "123456");
    },
    cancelProcess() {
      this.$refs.processCancel.show("123456", "123456");
    },
    showCheckPass() {
      this.$refs.checkPass.show();
    },
    checkSuccess() {
      this.$Message.success("验证成功");
    }
  },
  mounted() {
    this.init();
  }
};
</script>

<style lang="less">
.article {
  font-size: 16px;
  font-weight: 400;
  margin: 12px 0;
}
.blue {
  color: #40a9ff !important;
}
code {
  display: inline-block;
  background: #f7f7f7;
  font-family: Consolas, Monaco, Andale Mono, Ubuntu Mono, monospace;
  margin: 0 3px;
  padding: 1px 5px;
  border-radius: 3px;
  color: #666;
  border: 1px solid #eee;
}
</style>