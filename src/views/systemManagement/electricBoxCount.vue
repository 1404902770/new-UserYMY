<template>
  <!-- 系统管理 - 电箱计数 -->
  <div class="electricBoxCount">
    <el-container style="height: 100%;">
      <!-- 上部 -->
      <el-header>
        <div class="bread">
          <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>能耗分析</el-breadcrumb-item>
            <el-breadcrumb-item>线路计数</el-breadcrumb-item>
          </el-breadcrumb>
        </div>
      </el-header>
      <!-- 中部 -->
      <el-main>
        <div class="content">

          <div class="elebox">
            <p class="headtext">选择电箱
              <el-button
                type="primary"
                size="small"
                @click="getlines"
                :disabled="selectData.nid?false:true"
                style="float: right;margin-bottom: 10px;"
              >确定</el-button>
            </p>
            <!-- 电箱列表 -->
            <!-- @selection-change="handleSelectionChange" -->
            <el-table
              ref="multipleTable"
              :data="eletableData"
              tooltip-effect="dark"
              style="width: 100%"
              border
              :max-height="tableHeight"
              @selection-change="selectionChange"
              :header-cell-class-name="cellClass"
            >
              <el-table-column
                type="selection"
                align="center"
                min-width="50"
                show-overflow-tooltip
              ></el-table-column>

              <el-table-column
                prop="num"
                label="序号"
                align="center"
                min-width="50"
                show-overflow-tooltip
              ></el-table-column>

              <el-table-column
                prop="n_name"
                show-overflow-tooltip
                label="电箱名称"
                align="center"
                min-width="100"
              ></el-table-column>

              <el-table-column
                prop="nid"
                show-overflow-tooltip
                label="电箱号"
                align="center"
                min-width="100"
              >
                <template slot-scope="scope">
                  <span>{{ hexCharCodeToStr(scope.row.nid) }}</span>
                </template>
              </el-table-column>

              <el-table-column
                prop="n_local"
                show-overflow-tooltip
                label="安装地址"
                align="center"
                min-width="120"
              ></el-table-column>
            </el-table>
          </div>

          <div class="linebox">
            <p class="headtext">选择线路
              <el-button
                type="primary"
                size="small"
                style="float: right;margin-bottom: 10px;"
                @click="awitSubmit"
                :disabled="selectionBoxCount.length>0?false:true"
              >确定</el-button>
            </p>
            <el-table
              ref="multipleTable1"
              :data="linetableData"
              tooltip-effect="dark"
              style="width: 100%"
              border
              :max-height="tableHeight"
              @selection-change="handleSelectionChange"
            >
              <el-table-column
                type="selection"
                align="center"
                min-width="50"
                show-overflow-tooltip
              ></el-table-column>

              <el-table-column
                prop="num"
                label="序号"
                align="center"
                min-width="50"
                show-overflow-tooltip
              ></el-table-column>

              <el-table-column
                prop="name"
                show-overflow-tooltip
                label="线路名称"
                align="center"
                min-width="100"
              ></el-table-column>

              <el-table-column
                prop="nid"
                show-overflow-tooltip
                label="电箱号"
                align="center"
                min-width="100"
              >
                <template slot-scope="scope">
                  <span>{{ hexCharCodeToStr(scope.row.nid) }}</span>
                </template>
              </el-table-column>

              <el-table-column
                prop="mid"
                show-overflow-tooltip
                label="线路编号"
                align="center"
                min-width="100"
              >
                <template slot-scope="scope">
                  <span>{{ '线路' + scope.row.mid }}</span>
                </template>
              </el-table-column>

              <el-table-column
                prop="local"
                show-overflow-tooltip
                label="安装地址"
                align="center"
                min-width="120"
              ></el-table-column>
            </el-table>
          </div>

          <div class="selebox">
            <p class="headtext">已选线路计数
              <el-button
                type="primary"
                size="small"
                style="float: right;margin-bottom: 10px;"
                @click="determineAdd"
              >确定</el-button>
            </p>
            <!-- <el-collapse
              v-model="activeName"
              accordion
            >
              <el-collapse-item
                v-for="(item,index) in sureData1"
                :key="index"
                :title="item.n_name"
                :name="index"
              >
                <div>线路号：{{item.mid}}</div>
                <div>线路名称：{{item.name}}</div>
                <div>安装地址：{{item.local}}</div>
              </el-collapse-item>
            </el-collapse> -->
            <el-table
              ref="multipleTable1"
              :data="sureData1"
              tooltip-effect="dark"
              style="width: 100%"
              border
              :max-height="tableHeight"
            >
              <el-table-column
                prop="num"
                label="序号"
                align="center"
                min-width="50"
                show-overflow-tooltip
              ></el-table-column>

              <el-table-column
                prop="nid"
                show-overflow-tooltip
                label="电箱名称"
                align="center"
                min-width="100"
              >
                <template slot-scope="scope">
                  <span>{{ scope.row.n_name }}</span>
                </template>
              </el-table-column>

              <el-table-column
                prop="name"
                show-overflow-tooltip
                label="线路名称"
                align="center"
                min-width="100"
              ></el-table-column>

              <!-- <el-table-column
                prop="nid"
                show-overflow-tooltip
                label="电箱号"
                align="center"
                min-width="100"
              >
                <template slot-scope="scope">
                  <span>{{ hexCharCodeToStr(scope.row.nid) }}</span>
                </template>
              </el-table-column> -->

              <el-table-column
                prop="mid"
                show-overflow-tooltip
                label="线路编号"
                align="center"
                min-width="90"
              >
                <template slot-scope="scope">
                  <span>{{ '线路' + scope.row.mid }}</span>
                </template>
              </el-table-column>

              <el-table-column
                prop="local"
                show-overflow-tooltip
                label="安装地址"
                align="center"
                min-width="120"
              ></el-table-column>

              <el-table-column
                label="操作"
                align="center"
              >
                <template slot-scope="scope">
                  <el-button
                    @click="deleteRow(scope.$index, scope.row)"
                    type="text"
                    size="small"
                    style="color:#93F6F6"
                  >移除
                  </el-button>
                </template>
              </el-table-column>
            </el-table>
            <p style="color:#ccc;margin-top:20px">共计：{{sureData1?sureData1.length+"条":"0条"}}</p>
          </div>
        </div>
      </el-main>
      <!-- 尾部 -->
      <el-footer>
      </el-footer>
      <!-- 底部版权 -->
      <copyright></copyright>
    </el-container>

  </div>
</template>
  
  <script>
import copyright from "../../components/copyright";
export default {
  name: "electricBoxCount",
  components: {
    copyright
  },
  data() {
    return {
      tableHeight: '630',

      // 选择的电箱
      selectData: {},

      // 默认打开
      activeName: '',

      // 已选址线路中转
      sureData: [],
      // 已选线路
      sureData1: [],

      // 电箱列表数据
      eletableData: [],
      // 线路列表数据
      linetableData: [],
      // 已选择电箱数据
      multipleSelection: [],

      // 已选电箱计数
      selectionBoxCount: [],
      // 删除 - 已选电箱计数
      selectionBoxCountData: [],

      he: []
    };
  },
  methods: {
    // 备注：cellClass()方法主要给复选框那一列添加 类名为 ‘disabledCheck’
    cellClass(row) {
      if (row.columnIndex === 0) {
        return 'disabledCheck'
      }
    },

    // 获取线路列表
    getList(page) {
      let username = localStorage.getItem("username");
      let uid = localStorage.getItem(username + 'uid');
      let type = "post";
      let url = this.urlb + "/api3/device/getallHwUser";
      let data = {
        data: {
          uid: uid,
          // uid: localStorage.getItem('uid'),
          nid: this.selectData.nid,
          page: 1,
          size: 37,
        }
      };
      this.myAjax(type, url, data, res => {
        // console.log(res)
        // this.tableData = res.data.data;
        this.linetableData = res.data.data
        // res.data.data.forEach(val => {
        //   this.linetableData.push(JSON.parse(val))
        // })

        for (let i = 0; i < this.linetableData.length; i++) {
          // 电箱号
          // this.linetableData[i].nid = this.hexCharCodeToStr(this.linetableData[i].nid);
          // 线路类型
          if (this.linetableData[i].tt == '0') {
            this.linetableData[i].tt = '[F]总路单相';
          } else if (this.linetableData[i].tt == '1') {
            this.linetableData[i].tt = '[F]总路三相';
          } else if (this.linetableData[i].tt == '10') {
            this.linetableData[i].tt = '[F]支路单相';
          } else if (this.linetableData[i].tt == '11') {
            this.linetableData[i].tt = '[F]支路三相';
          } else if (this.linetableData[i].tt == '80') {
            this.linetableData[i].tt = '[T]总路单相';
          } else if (this.linetableData[i].tt == '81') {
            this.linetableData[i].tt = '[T]总路三相';
          } else if (this.linetableData[i].tt == '90') {
            this.linetableData[i].tt = '[T]支路单相';
          } else {
            this.linetableData[i].tt = '[T]支路三相';
          }
          this.linetableData[i].num = i + 1
          this.linetableData[i].nid = this.selectData.nid
          // this.linetableData[i].aa = parseInt(this.linetableData[i].aa, 16)
        }
      });
    },
    // 初始化获取下拉数据 - 所有电箱
    getOptions() {
      let username = localStorage.getItem("username");
      let uid = localStorage.getItem(username + 'uid');
      let type = "post";
      let url = this.urlb + "/api3/EnergyData/getNetdSimpleList";
      let data = {
        data: {
          uid: uid
          // uid: localStorage.getItem('uid')
        }
      };
      this.myAjax(type, url, data, res => {
        this.eletableData = res.data.data
        // this.getList(1)

        for (let i = 0; i < this.eletableData.length; i++) {
          this.eletableData[i].num = i + 1
        }
      });
    },

    // 表格的选中 可以获得当前选中的数据
    selectionChange(val) {
      // 将选中的数据存储起来
      this.selectData = val[0]
      if (val.length > 1) {
        this.$refs.multipleTable.clearSelection();
        this.$refs.multipleTable.toggleRowSelection(val.pop());
      }
      // console.log(this.selectData)
    },

    // 确认是否选择电箱
    getlines() {
      this.$confirm('请确认是否对此电箱下线路进行计数', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        if (this.selectData.nid) {
          this.getList(1)
        } else {
          this.$message({
            type: 'warning',
            message: '请选择电箱'
          });
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消'
        });
      });
    },

    // 是否将设备放入待计数区
    awitSubmit() {
      this.$confirm('是否将设备放入待计数区', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.selectionBoxCount.forEach(value => {
          this.multipleSelection.push(value)
        })
        this.$store.commit("setcountData", this.multipleSelection)

        this.sureData = this.$store.state.countData
        this.sureData1 = []
        this.sureData.forEach(val => {
          this.sureData1.push(val)
        })

        for (let i = 0; i < this.sureData1.length; i++) {
          this.sureData1[i].num = i + 1
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
      // console.log(this.$store.state.countData)

      // console.log(this.sureData)
    },

    // 移除待计数区设备
    deleteRow(index, row) {
      // console.log(index, row)
      this.$confirm('此操作将移除线路计数?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.sureData1.splice(index, 1)
        // console.log(this.sureData1)
        this.$message({
          type: 'success',
          message: '移除成功!'
        });
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消移除'
        });
      });
    },

    // 初始化获取 - 已选电箱计数
    getSelectionBoxCount(page) {
      let username = localStorage.getItem("username");
      let uid = localStorage.getItem(username + 'uid');
      let type = "post";
      let url = this.urlb + "/api3/device/totalAaList";
      let data = {
        data: {
          uid: uid,
          // uid: localStorage.getItem("uid"),
          gid: localStorage.getItem("gid"),
        }
      };
      this.myAjax(type, url, data, res => {
        // console.log(res.data.data)

        if (res.data.code == 705) {
          this.$message.warning("您还未选择线路计数!");
        } else {
          this.sureData1 = res.data.data

          for (let i = 0; i < this.sureData1.length; i++) {
            this.sureData1[i].num = i + 1
          }
        }

        // console.log(JSON.parse(res.data.data))
        // 这里后期会改动 - 后台如果res.data[0]做了处理，删除||后边的
        // let arr1 = [];
        // JSON.parse(res.data.data).map(val => {
        //   if (val != false) {
        //     arr1.push(val);
        //   }
        // });
        // if (res.data.data.code == 36) {
        //   this.zcode = 36;
        //   this.show3 = true;
        //   this.$message.warning("您还未选择电箱计数!");
        // } else if (arr1.length < 1) {
        //   this.zcode = 36;
        //   this.show3 = true;
        //   this.$message.warning("您还未选择电箱计数!");
        // } else {
        //   this.selectionBoxCount = arr1;
        //   this.show3 = false;
        // }
      });
    },

    // 列表选择 - √
    handleSelectionChange(val) {
      // let a = new Set(this.zlist);
      // let b = new Set(val);
      // let he = new Set([...b].filter(x => !a.has(x)));
      // this.multipleSelection = Array.from(he)

      this.selectionBoxCount = val

      // console.log(this.multipleSelection)
    },

    // 点击 - 确定添加电箱计数
    determineAdd() {
      this.$confirm("是否把已选择的线路加入线路计数?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        if (this.sureData1.length > 0) {
          let arrId = [];
          for (let i = 0; i < this.sureData1.length; i++) {
            arrId.push(this.sureData1[i].nid + '-' + this.sureData1[i].mid);
          }
          let arrIdString = this.arrToString(arrId);

          let username = localStorage.getItem("username");
          let uid = localStorage.getItem(username + 'uid');
          let type = "post";
          let url = this.urlb + "/api3/device/setTotalAa";
          let data = {
            data: {
              uid: uid,
              // uid: localStorage.getItem("uid"),
              gid: localStorage.getItem("gid"),
              dto: arrIdString
            }
          };
          this.myAjax(type, url, data, res => {
            // console.log(res)
            if (res.data.code == 500) {
              let _this = this;
              _this.$message.success("添加成功!...3秒后刷新更新数据,如操作成功没有刷新数据请手动刷新");
              setTimeout(() => {
                // _this.getList(1);
                _this.getSelectionBoxCount();
              }, 3000)
            } else {
              this.$message.warning("可是出现了重复,添加失败!");
            }
          });
        } else {
          this.$message({
            type: "warning",
            message: "请选择线路"
          });
        }
      }).catch(() => {
        this.$message({
          type: "info",
          message: "已取消操作"
        });
      });
    },
  },

  computed: {},
  mounted() {
    this.$refs.multipleTable.$el.style.width = '99.99%'
    this.$refs.multipleTable1.$el.style.width = '99.99%'

    this.tableHeight = window.innerHeight - this.$refs.multipleTable.$el.offsetTop - 106;
    this.tableHeight = window.innerHeight - this.$refs.multipleTable1.$el.offsetTop - 106;
  },
  created() {
    this.getOptions(1);
    this.getSelectionBoxCount();
  }
};
</script>
  
 <style lang="less" scoped>
.electricBoxCount .el-header .el-form /deep/ .el-radio-button__inner {
  padding: 7px 10px !important;
}
.electricBoxCount /deep/ .el-dialog__body {
  padding-right: 65px;
}
.electricBoxCount {
  overflow: hidden;
  height: 100%;
  .el-header {
    text-align: right;
    line-height: 60px;
    .el-button {
      margin-top: 3px;
      padding: 9px 20px;
    }
    .bread {
      display: inline-block;
      float: left;
      margin-top: 22px;
      .el-breadcrumb /deep/ .el-breadcrumb__inner {
        color: #ccc;
      }
    }
    .el-form {
      text-align: left;
      margin-top: 10px;
      .el-form-item {
        float: left;
      }
      .el-button {
        padding: 9px 20px;
        margin-top: 3px;
        float: right;
      }
      .dianxiang {
        width: 360px;
        .el-select {
          width: 100%;
        }
      }
    }
  }
  .el-main {
    color: #333;
    text-align: center;
    padding: 0;
    height: 100%;
    .content {
      width: 100%;
      display: flex;
      justify-content: space-around;
      .headtext {
        color: #ccc;
        font-size: 16px;
        font-weight: bold;
        margin-bottom: 10px;
      }
    }
    .elebox,
    .linebox,
    .selebox {
      width: 32%;
      height: 60px;
    }

    /* 去掉全选按钮 */
    .elebox /deep/ .el-table .disabledCheck .cell .el-checkbox__inner {
      display: none !important;
    }

    .elebox /deep/ .el-table .disabledCheck .cell::before {
      content: "选择";
      text-align: center;
      // line-height: 37px;
    }

    .selebox {
      .el-collapse {
        border-color: #5b93ff;
        border-left: 1px solid #5b93ff;
        border-right: 1px solid #5b93ff;
        .el-collapse-item {
          text-align: left;
        }
        .el-collapse-item /deep/ .el-collapse-item__header {
          color: #ccc;
          background: rgba(0, 0, 0, 0.2);
          border-bottom-color: #5b93ff;
        }
        .el-collapse-item /deep/ .el-collapse-item__content {
          color: #ccc;
          padding: 0 10px;
          box-sizing: border-box;
          background: #27428e;
        }
        .el-collapse-item /deep/ .el-collapse-item__wrap {
          border-bottom-color: #5b93ff;
        }
      }
    }
  }
  .el-footer {
  }
}
</style>
  