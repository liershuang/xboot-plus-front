<style lang="less">
@import "../../../styles/table-common.less";
@import "./userManage.less";
</style>
<template>
  <div class="search">
    <Card>
      <Row v-show="openSearch" @keydown.enter.native="handleSearch">
        <Form ref="searchForm" :model="searchForm" inline :label-width="70">
          <Form-item label="用户名称" prop="username">
            <Input
              type="text"
              v-model="searchForm.username"
              clearable
              placeholder="请输入用户名"
              style="width: 200px"
            />
          </Form-item>
          <Form-item label="部门" prop="department">
            <department-choose @on-change="handleSelectDep" style="width: 200px" ref="dep"></department-choose>
          </Form-item>
          <span v-if="drop">
            <Form-item label="手机号" prop="mobile">
              <Input
                type="text"
                v-model="searchForm.mobile"
                clearable
                placeholder="请输入手机号"
                style="width: 200px"
              />
            </Form-item>
            <Form-item label="邮箱" prop="email">
              <Input
                type="text"
                v-model="searchForm.email"
                clearable
                placeholder="请输入邮箱"
                style="width: 200px"
              />
            </Form-item>
            <Form-item label="性别" prop="sex">
              <Select v-model="searchForm.sex" placeholder="请选择" clearable style="width: 200px">
                <Option v-for="(item, i) in dictSex" :key="i" :value="item.value">{{item.title}}</Option>
              </Select>
            </Form-item>
            <Form-item label="用户类型" prop="type">
              <Select v-model="searchForm.type" placeholder="请选择" clearable style="width: 200px">
                <Option value="0">普通用户</Option>
                <Option value="1">管理员</Option>
              </Select>
            </Form-item>
            <Form-item label="用户状态" prop="status">
              <Select v-model="searchForm.status" placeholder="请选择" clearable style="width: 200px">
                <Option value="0">正常</Option>
                <Option value="-1">禁用</Option>
              </Select>
            </Form-item>
            <Form-item label="创建时间">
              <DatePicker
                v-model="selectDate"
                type="daterange"
                format="yyyy-MM-dd"
                clearable
                @on-change="selectDateRange"
                placeholder="选择起始时间"
                style="width: 200px"
              ></DatePicker>
            </Form-item>
          </span>
          <Form-item style="margin-left:-35px;" class="br">
            <Button @click="handleSearch" type="primary" icon="ios-search">搜索</Button>
            <Button @click="handleReset">重置</Button>
            <a class="drop-down" @click="dropDown">
              {{dropDownContent}}
              <Icon :type="dropDownIcon"></Icon>
            </a>
          </Form-item>
        </Form>
      </Row>
      <Row class="operation">
        <Button @click="add" type="primary" icon="md-add">添加用户</Button>
        <Button @click="delAll" icon="md-trash">批量删除</Button>
        <Dropdown @on-click="handleDropdown">
          <Button>
            更多操作
            <Icon type="md-arrow-dropdown" />
          </Button>
          <DropdownMenu slot="list">
            <DropdownItem name="refresh">刷新</DropdownItem>
            <DropdownItem name="reset">重置用户密码</DropdownItem>
            <DropdownItem name="exportData">导出所选数据</DropdownItem>
            <DropdownItem name="exportAll">导出全部数据</DropdownItem>
            <DropdownItem name="importData">导入数据(付费)</DropdownItem>
          </DropdownMenu>
        </Dropdown>
        <Button type="dashed" @click="openSearch=!openSearch">{{openSearch ? "关闭搜索" : "开启搜索"}}</Button>
        <Button type="dashed" @click="openTip=!openTip">{{openTip ? "关闭提示" : "开启提示"}}</Button>
      </Row>
      <Row v-show="openTip">
        <Alert show-icon>
          已选择
          <span class="select-count">{{selectCount}}</span> 项
          <a class="select-clear" @click="clearSelectAll">清空</a>
        </Alert>
      </Row>
      <Row>
        <Table
          :loading="loading"
          border
          :columns="columns"
          :data="data"
          sortable="custom"
          @on-sort-change="changeSort"
          @on-selection-change="showSelect"
          ref="table"
        ></Table>
      </Row>
      <Row type="flex" justify="end" class="page">
        <Page
          :current="searchForm.pageNumber"
          :total="total"
          :page-size="searchForm.pageSize"
          @on-change="changePage"
          @on-page-size-change="changePageSize"
          :page-size-opts="[10,20,50]"
          size="small"
          show-total
          show-elevator
          show-sizer
        ></Page>
      </Row>
    </Card>

    <Modal
      :title="modalTitle"
      v-model="userModalVisible"
      :mask-closable="false"
      :width="500"
      :styles="{top: '30px'}"
    >
      <Form ref="form" :model="form" :label-width="70" :rules="formValidate">
        <FormItem label="用户名" prop="username">
          <Input v-model="form.username" autocomplete="off" />
        </FormItem>
        <FormItem label="密码" prop="password" v-if="modalType==0" :error="errorPass">
          <Input type="password" password v-model="form.password" autocomplete="off" />
        </FormItem>
        <FormItem label="邮箱" prop="email">
          <Input v-model="form.email" />
        </FormItem>
        <FormItem label="手机号" prop="mobile">
          <Input v-model="form.mobile" />
        </FormItem>
        <FormItem label="性别" prop="sex">
          <RadioGroup v-model="form.sex">
            <Radio v-for="(item, i) in dictSex" :key="i" :label="item.value">{{item.title}}</Radio>
          </RadioGroup>
        </FormItem>
        <Form-item label="头像" prop="avatar">
          <upload-pic-input v-model="form.avatar"></upload-pic-input>
        </Form-item>
        <Form-item label="所属部门">
          <department-tree-choose @on-change="handleSelectDepTree" ref="depTree"></department-tree-choose>
        </Form-item>
        <FormItem label="用户类型" prop="type">
          <Select v-model="form.type" placeholder="请选择">
            <Option :value="0">普通用户</Option>
            <Option :value="1">管理员</Option>
          </Select>
        </FormItem>
        <FormItem label="角色分配" prop="roles">
          <Select v-model="form.roles" multiple>
            <Option v-for="item in roleList" :value="item.id" :key="item.id" :label="item.name">
              <span style="margin-right:10px;">{{ item.name }}</span>
              <span style="color:#ccc;">{{ item.description }}</span>
            </Option>
          </Select>
        </FormItem>
      </Form>
      <div slot="footer">
        <Button type="text" @click="cancelUser">取消</Button>
        <Button type="primary" :loading="submitLoading" @click="submitUser">提交</Button>
      </div>
    </Modal>
    <!-- 自定义导出数据 -->
    <Modal
      v-model="exportModalVisible"
      :title="exportTitle"
      :loading="loadingExport"
      @on-ok="exportCustomData"
    >
      <Form ref="exportForm" :label-width="100">
        <FormItem label="导出文件名">
          <Input v-model="filename" />
        </FormItem>
        <FormItem label="自定义导出列">
          <CheckboxGroup v-model="chooseColumns">
            <Checkbox
              v-for="(item, i) in exportColumns"
              :label="item.title"
              :key="i"
              :value="item.checked"
              :disabled="item.disabled"
            ></Checkbox>
          </CheckboxGroup>
        </FormItem>
      </Form>
    </Modal>
    <Drawer title="导入数据" closable v-model="importModalVisible" width="1000">
      <div style="display:flex;justify-content: space-between;align-items: center;">
        <Upload action :before-upload="beforeUploadImport" accept=".xls, .xlsx">
          <Button
            :loading="reading"
            icon="ios-cloud-upload-outline"
            style="margin-right:10px"
          >上传Excel文件</Button>
          <span v-if="uploadfile.name">当前选择文件：{{ uploadfile.name }}</span>
        </Upload>
        <Button @click="clearImportData" icon="md-trash">清空数据</Button>
      </div>
      <Alert type="warning" show-icon>导入前请下载查看导入模版数据文件，确保数据格式正确，不得修改列英文名称。必需数据字段：用户名(唯一)、密码(明文)</Alert>
      <Table
        :columns="importColumns"
        border
        :height="height"
        :data="importTableData"
        ref="importTable"
      ></Table>
      <div class="drawer-footer">
        <Button @click="downloadTemple" type="info" style="position:absolute;left:15px;">下载导入模板</Button>
        <Button @click="importModalVisible=false">关闭</Button>
        <Button
          :loading="importLoading"
          :disabled="importTableData.length<=0"
          @click="importData"
          style="margin-left:5px"
          type="primary"
        >
          确认导入
          <span v-if="importTableData.length>0">{{importTableData.length}} 条数据</span>
        </Button>
      </div>
    </Drawer>

    <check-password ref="checkPass" @on-success="resetPass" />
  </div>
</template>

<script>
import {
  getUserListData,
  getAllRoleList,
  addUser,
  editUser,
  enableUser,
  disableUser,
  deleteUser,
  getAllUserData,
  importUserData,
  resetUserPass
} from "@/api/index";
import expandRow from "./expand.vue";
import { validateMobile, validatePassword } from "@/libs/validate";
import departmentChoose from "@/views/my-components/xboot/department-choose";
import departmentTreeChoose from "@/views/my-components/xboot/department-tree-choose";
import uploadPicInput from "@/views/my-components/xboot/upload-pic-input";
import checkPassword from "@/views/my-components/xboot/check-password";
// 模版导入文件表数据
import { userColumns, userData } from "@/libs/importTemplate";
// 指定导出列数据
import { exportColumn } from "./exportColumn";
import excel from "@/libs/excel";
export default {
  name: "user-manage",
  components: {
    expandRow,
    departmentChoose,
    departmentTreeChoose,
    uploadPicInput,
    checkPassword
  },
  data() {
    return {
      height: 510,
      openSearch: true,
      openTip: true,
      loading: true,
      reading: false,
      importLoading: false,
      loadingExport: true,
      exportModalVisible: false,
      importModalVisible: false,
      drop: false,
      dropDownContent: "展开",
      dropDownIcon: "ios-arrow-down",
      selectCount: 0,
      selectList: [],
      searchForm: {
        username: "",
        departmentId: "",
        mobile: "",
        email: "",
        sex: "",
        type: "",
        status: "",
        pageNumber: 1,
        pageSize: 10,
        sort: "createTime",
        order: "desc",
        startDate: "",
        endDate: ""
      },
      selectDate: null,
      modalType: 0,
      userModalVisible: false,
      modalTitle: "",
      form: {
        username: "",
        mobile: "",
        email: "",
        sex: "",
        type: 0,
        roles: [],
        departmentId: "",
        departmentTitle: ""
      },
      userRoles: [],
      roleList: [],
      errorPass: "",
      formValidate: {
        username: [
          { required: true, message: "账号不能为空", trigger: "blur" }
        ],
        mobile: [
          { required: true, message: "手机号不能为空", trigger: "blur" },
          { validator: validateMobile, trigger: "blur" }
        ],
        email: [
          { required: true, message: "请输入邮箱地址" },
          { type: "email", message: "邮箱格式不正确" }
        ]
      },
      submitLoading: false,
      columns: [
        {
          type: "selection",
          width: 60,
          align: "center",
          fixed: "left"
        },
        {
          type: "expand",
          width: 50,
          fixed: "left",
          render: (h, params) => {
            return h(expandRow, {
              props: {
                row: params.row
              }
            });
          }
        },
        {
          type: "index",
          width: 60,
          align: "center",
          fixed: "left"
        },
        {
          title: "用户名",
          key: "username",
          minWidth: 145,
          sortable: true,
          fixed: "left"
        },
        {
          title: "头像",
          key: "avatar",
          width: 80,
          align: "center",
          render: (h, params) => {
            return h("Avatar", {
              props: {
                src: params.row.avatar
              }
            });
          }
        },
        {
          title: "所属部门",
          key: "departmentTitle",
          width: 120
        },
        {
          title: "手机",
          key: "mobile",
          width: 125,
          sortable: true,
          render: (h, params) => {
            if (this.getStore("roles").includes("ROLE_ADMIN")) {
              return h("span", params.row.mobile);
            } else {
              return h("span", "您无权查看该数据");
            }
          }
        },
        {
          title: "邮箱",
          key: "email",
          width: 180,
          sortable: true
        },
        {
          title: "性别",
          key: "sex",
          width: 70,
          align: "center"
        },
        {
          title: "用户类型",
          key: "type",
          align: "center",
          width: 100,
          render: (h, params) => {
            let re = "";
            if (params.row.type == 1) {
              re = "管理员";
            } else if (params.row.type == 0) {
              re = "普通用户";
            }
            return h("div", re);
          }
        },
        {
          title: "状态",
          key: "status",
          align: "center",
          width: 110,
          render: (h, params) => {
            if (params.row.status == 0) {
              return h("div", [
                h("Badge", {
                  props: {
                    status: "success",
                    text: "正常启用"
                  }
                })
              ]);
            } else if (params.row.status == -1) {
              return h("div", [
                h("Badge", {
                  props: {
                    status: "error",
                    text: "禁用"
                  }
                })
              ]);
            }
          },
          filters: [
            {
              label: "正常启用",
              value: 0
            },
            {
              label: "禁用",
              value: -1
            }
          ],
          filterMultiple: false,
          filterMethod(value, row) {
            if (value == 0) {
              return row.status == 0;
            } else if (value == -1) {
              return row.status == -1;
            }
          }
        },
        {
          title: "创建时间",
          key: "createTime",
          sortable: true,
          sortType: "desc",
          width: 180
        },
        {
          title: "操作",
          key: "action",
          width: 200,
          align: "center",
          fixed: "right",
          render: (h, params) => {
            let enableOrDisable = "";
            if (params.row.status == 0) {
              enableOrDisable = h(
                "Button",
                {
                  props: {
                    size: "small"
                  },
                  style: {
                    marginRight: "5px"
                  },
                  on: {
                    click: () => {
                      this.disable(params.row);
                    }
                  }
                },
                "禁用"
              );
            } else {
              enableOrDisable = h(
                "Button",
                {
                  props: {
                    type: "success",
                    size: "small"
                  },
                  style: {
                    marginRight: "5px"
                  },
                  on: {
                    click: () => {
                      this.enable(params.row);
                    }
                  }
                },
                "启用"
              );
            }
            return h("div", [
              h(
                "Button",
                {
                  props: {
                    type: "primary",
                    size: "small"
                  },
                  style: {
                    marginRight: "5px"
                  },
                  on: {
                    click: () => {
                      this.edit(params.row);
                    }
                  }
                },
                "编辑"
              ),
              enableOrDisable,
              h(
                "Button",
                {
                  props: {
                    type: "error",
                    size: "small"
                  },
                  on: {
                    click: () => {
                      this.remove(params.row);
                    }
                  }
                },
                "删除"
              )
            ]);
          }
        }
      ],
      exportColumns: exportColumn,
      chooseColumns: [],
      filename: "用户数据",
      exportTitle: "确认导出",
      exportType: "",
      importTableData: [],
      importColumns: [],
      uploadfile: {
        name: ""
      },
      tempColumns: userColumns,
      tempData: userData,
      data: [],
      exportData: [],
      total: 0,
      dictSex: this.$store.state.dict.sex
    };
  },
  methods: {
    init() {
      this.getUserList();
      // 初始化导出列数据
      let array = [];
      this.exportColumns.forEach(e => {
        // 指定列限制权限
        if (
          !this.getStore("roles").includes("ROLE_ADMIN") &&
          e.key == "mobile"
        ) {
          e.title = "[无权导出] " + e.title;
          e.disabled = true;
        } else {
          e.disabled = false;
        }
        array.push(e.title);
      });
      this.chooseColumns = array;
    },
    handleSelectDepTree(v) {
      this.form.departmentId = v;
    },
    handleSelectDep(v) {
      this.searchForm.departmentId = v;
    },
    changePage(v) {
      this.searchForm.pageNumber = v;
      this.getUserList();
      this.clearSelectAll();
    },
    changePageSize(v) {
      this.searchForm.pageSize = v;
      this.getUserList();
    },
    selectDateRange(v) {
      if (v) {
        this.searchForm.startDate = v[0];
        this.searchForm.endDate = v[1];
      }
    },
    getUserList() {
      // 多条件搜索用户列表
      this.loading = true;
      // 避免后台默认值
      if (!this.searchForm.type) {
        this.searchForm.type = "";
      }
      if (!this.searchForm.status) {
        this.searchForm.status = "";
      }
      getUserListData(this.searchForm).then(res => {
        this.loading = false;
        if (res.success) {
          this.data = res.result.content;
          this.total = res.result.totalElements;
        }
      });
    },
    handleSearch() {
      this.searchForm.pageNumber = 1;
      this.searchForm.pageSize = 10;
      this.getUserList();
    },
    handleReset() {
      this.$refs.searchForm.resetFields();
      this.searchForm.pageNumber = 1;
      this.searchForm.pageSize = 10;
      this.selectDate = null;
      this.searchForm.startDate = "";
      this.searchForm.endDate = "";
      this.$refs.dep.clearSelect();
      this.searchForm.departmentId = "";
      // 重新加载数据
      this.getUserList();
    },
    changeSort(e) {
      this.searchForm.sort = e.key;
      this.searchForm.order = e.order;
      if (e.order == "normal") {
        this.searchForm.order = "";
      }
      this.getUserList();
    },
    getRoleList() {
      getAllRoleList().then(res => {
        if (res.success) {
          this.roleList = res.result;
        }
      });
    },
    handleDropdown(name) {
      if (name == "refresh") {
        this.getUserList();
      } else if (name == "reset") {
        if (this.selectCount <= 0) {
          this.$Message.warning("您还未选择要重置密码的用户");
          return;
        }
        this.$refs.checkPass.show();
      } else if (name == "exportData") {
        if (this.selectCount <= 0) {
          this.$Message.warning("您还未选择要导出的数据");
          return;
        }
        this.exportType = "part";
        this.exportModalVisible = true;
        this.exportTitle = "确认导出 " + this.selectCount + " 条数据(付费)";
      } else if (name == "exportAll") {
        this.exportType = "all";
        this.exportModalVisible = true;
        this.exportTitle = "确认导出全部 " + this.total + " 条数据(付费)";
        getAllUserData().then(res => {
          if (res.success) {
            this.exportData = res.result;
          }
        });
      } else if (name == "importData") {
        this.importModalVisible = true;
      }
    },
    resetPass() {
      this.$Modal.confirm({
        title: "确认重置",
        content:
          "您确认要重置所选的 " +
          this.selectCount +
          " 条用户数据密码为【123456】?",
        loading: true,
        onOk: () => {
          let ids = "";
          this.selectList.forEach(function(e) {
            ids += e.id + ",";
          });
          ids = ids.substring(0, ids.length - 1);
          resetUserPass({ ids: ids }).then(res => {
            this.$Modal.remove();
            if (res.success) {
              this.$Message.success("操作成功");
              this.clearSelectAll();
              this.getUserList();
            }
          });
        }
      });
    },
    exportCustomData() {
      if (this.filename == "") {
        this.filename = "用户数据";
      }
      // 判断勾选导出列
      let array = [];
      this.exportColumns.forEach(e => {
        this.chooseColumns.forEach(c => {
          if (e.title == c && !e.disabled) {
            array.push(e);
          }
        });
      });
      this.exportColumns = array;
      this.exportModalVisible = false;
      let title = [];
      let key = [];
      this.exportColumns.forEach(e => {
        title.push(e.title);
        key.push(e.key);
      });
      const params = {
        title: title,
        key: key,
        data: this.exportData,
        autoWidth: true,
        filename: this.filename
      };
      excel.export_array_to_excel(params);
    },
    beforeUploadImport(file) {
      this.uploadfile = file;
      const fileExt = file.name
        .split(".")
        .pop()
        .toLocaleLowerCase();
      if (fileExt == "xlsx" || fileExt == "xls") {
        this.readFile(file);
        this.file = file;
      } else {
        this.$Notice.warning({
          title: "文件类型错误",
          desc:
            "所选文件‘ " +
            file.name +
            " ’不是EXCEL文件，请选择后缀为.xlsx或者.xls的EXCEL文件。"
        });
      }
      return false;
    },
    // 读取文件
    readFile(file) {
      this.reading = true;
      const reader = new FileReader();
      reader.readAsArrayBuffer(file);
      reader.onerror = e => {
        this.reading = false;
        this.$Message.error("文件读取出错");
      };
      reader.onload = e => {
        const data = e.target.result;
        const { header, results } = excel.read(data, "array");
        const tableTitle = header.map(item => {
          return { title: item, key: item, minWidth: 100, align: "center" };
        });
        this.importTableData = results;
        this.importColumns = tableTitle;
        this.reading = false;
        this.$Message.success("读取数据成功");
      };
    },
    clearImportData() {
      this.importTableData = [];
      this.importColumns = [];
      this.uploadfile = {};
    },
    downloadTemple() {
      let title = [];
      let key = [];
      userColumns.forEach(e => {
        title.push(e.title);
        key.push(e.key);
      });
      const params = {
        title: title,
        key: key,
        data: userData,
        autoWidth: true,
        filename: "导入用户数据模版"
      };
      excel.export_array_to_excel(params);
    },
    importData() {
      this.importLoading = true;
      importUserData(this.importTableData).then(res => {
        this.importLoading = false;
        if (res.success) {
          this.importModalVisible = false;
          this.getUserList();
          this.$Modal.info({
            title: "导入结果",
            content: res.message
          });
        }
      });
    },
    cancelUser() {
      this.userModalVisible = false;
    },
    submitUser() {
      this.$refs.form.validate(valid => {
        if (valid) {
          if (this.modalType == 0) {
            // 添加用户 避免编辑后传入id
            delete this.form.id;
            delete this.form.status;
            if (this.form.password == "" || this.form.password == undefined) {
              this.errorPass = "密码不能为空";
              return;
            }
            if (this.form.password.length < 6) {
              this.errorPass = "密码长度不得少于6位";
              return;
            }
            this.submitLoading = true;
            addUser(this.form).then(res => {
              this.submitLoading = false;
              if (res.success) {
                this.$Message.success("操作成功");
                this.getUserList();
                this.userModalVisible = false;
              }
            });
          } else {
            // 编辑
            this.submitLoading = true;
            editUser(this.form).then(res => {
              this.submitLoading = false;
              if (res.success) {
                this.$Message.success("操作成功");
                this.getUserList();
                this.userModalVisible = false;
              }
            });
          }
        }
      });
    },
    add() {
      this.modalType = 0;
      this.modalTitle = "添加用户";
      this.$refs.form.resetFields();
      this.$refs.depTree.setData("", "");
      this.userModalVisible = true;
    },
    edit(v) {
      this.modalType = 1;
      this.modalTitle = "编辑用户";
      this.$refs.form.resetFields();
      // 转换null为""
      for (let attr in v) {
        if (v[attr] == null) {
          v[attr] = "";
        }
      }
      let str = JSON.stringify(v);
      let data = JSON.parse(str);
      this.form = data;
      this.$refs.depTree.setData(data.departmentId, data.departmentTitle);
      let selectRolesId = [];
      this.form.roles.forEach(function(e) {
        selectRolesId.push(e.id);
      });
      this.form.roles = selectRolesId;
      this.userModalVisible = true;
    },
    enable(v) {
      this.$Modal.confirm({
        title: "确认启用",
        content: "您确认要启用用户 " + v.username + " ?",
        loading: true,
        onOk: () => {
          enableUser(v.id).then(res => {
            this.$Modal.remove();
            if (res.success) {
              this.$Message.success("操作成功");
              this.getUserList();
            }
          });
        }
      });
    },
    disable(v) {
      this.$Modal.confirm({
        title: "确认禁用",
        content: "您确认要禁用用户 " + v.username + " ?",
        loading: true,
        onOk: () => {
          disableUser(v.id).then(res => {
            this.$Modal.remove();
            if (res.success) {
              this.$Message.success("操作成功");
              this.getUserList();
            }
          });
        }
      });
    },
    remove(v) {
      this.$Modal.confirm({
        title: "确认删除",
        content: "您确认要删除用户 " + v.username + " ?",
        loading: true,
        onOk: () => {
          deleteUser(v.id).then(res => {
            this.$Modal.remove();
            if (res.success) {
              this.$Message.success("删除成功");
              this.getUserList();
            }
          });
        }
      });
    },
    dropDown() {
      if (this.drop) {
        this.dropDownContent = "展开";
        this.dropDownIcon = "ios-arrow-down";
      } else {
        this.dropDownContent = "收起";
        this.dropDownIcon = "ios-arrow-up";
      }
      this.drop = !this.drop;
    },
    showSelect(e) {
      this.exportData = e;
      this.selectList = e;
      this.selectCount = e.length;
    },
    clearSelectAll() {
      this.$refs.table.selectAll(false);
    },
    delAll() {
      if (this.selectCount <= 0) {
        this.$Message.warning("您还未选择要删除的数据");
        return;
      }
      this.$Modal.confirm({
        title: "确认删除",
        content: "您确认要删除所选的 " + this.selectCount + " 条数据?",
        loading: true,
        onOk: () => {
          let ids = "";
          this.selectList.forEach(function(e) {
            ids += e.id + ",";
          });
          ids = ids.substring(0, ids.length - 1);
          deleteUser(ids).then(res => {
            this.$Modal.remove();
            if (res.success) {
              this.$Message.success("删除成功");
              this.clearSelectAll();
              this.getUserList();
            }
          });
        }
      });
    }
  },
  mounted() {
    // 计算高度
    this.height = Number(document.documentElement.clientHeight - 230);
    this.init();
    this.getRoleList();
  }
};
</script>