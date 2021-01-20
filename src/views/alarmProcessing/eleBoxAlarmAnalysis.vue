<template>
  <!-- 综合报警 - 电箱警分析 -->
  <div class="alarmProcessingwy">
    <el-container style="height: 100%;">
      <!-- 上部 -->
      <el-header>
        <el-tabs
          v-model="activeName"
          @tab-click="handleClick"
        >
          <el-tab-pane
            label="报警排名"
            name="first"
          ></el-tab-pane>
          <el-tab-pane
            label="报警分析"
            name="second"
          ></el-tab-pane>
        </el-tabs>
      </el-header>
      <!-- 中部 -->
      <el-main>

        <div class="infobar">
          <div class="item">
            <div>
              <img
                src="../../../public/static/images/ri.png"
                alt=""
                style="width:60px;"
              >
            </div>
            <div class="numbox">
              <p class="numtext">今日报警数</p>
              <p class="num">{{todayMonthYear.today?todayMonthYear.today.today:0}}</p>
            </div>
            <div class="numbox">
              <p class="numtext">昨日报警数</p>
              <p class="num">{{todayMonthYear.today?todayMonthYear.today.yestoday:0}}</p>
            </div>
            <div class="numbox">
              <p class="numtext">环比</p>
              <p class="num">{{todayMonthYear.today?todayMonthYear.today.huanbi:0}}</p>
            </div>
          </div>
          <div class="item">
            <div>
              <img
                src="../../../public/static/images/yue.png"
                alt=""
                style="width:60px;"
              >
            </div>
            <div class="numbox">
              <p class="numtext">本月报警数</p>
              <p class="num">{{todayMonthYear.month?todayMonthYear.month.benyue:0}}</p>
            </div>
            <div class="numbox">
              <p class="numtext">上月报警数</p>
              <p class="num">{{todayMonthYear.month?todayMonthYear.month.shangyue:0}}</p>
            </div>
            <div class="numbox">
              <p class="numtext">环比</p>
              <p class="num">{{todayMonthYear.month?todayMonthYear.month.huanbi:0}}</p>
            </div>
          </div>
          <div class="item">
            <div>
              <img
                src="../../../public/static/images/nian.png"
                alt=""
                style="width:60px;"
              >
            </div>
            <div class="numbox">
              <p class="numtext">今年报警数</p>
              <p class="num">{{todayMonthYear.year?todayMonthYear.year.jinnain:0}}</p>
            </div>
            <div class="numbox">
              <p class="numtext">去年报警数</p>
              <p class="num">{{todayMonthYear.year?todayMonthYear.year.zuonian:0}}</p>
            </div>
            <div class="numbox">
              <p class="numtext">环比</p>
              <p class="num">{{todayMonthYear.year?todayMonthYear.year.huanbi:0}}</p>
            </div>
          </div>
        </div>
        <!-- 列表 -->
        <div
          v-show="activeName=='first'?true:false"
          class="tablebox onebox"
        >
          <!-- 电箱排名 -->
          <div style="width:46%">
            <el-form
              ref="form"
              :model="form"
              class="demo-ruleForm"
              label-width="90px"
            >
              <el-form-item
                label="选择时间"
                prop="p1"
              >
                <el-date-picker
                  v-model="form.dateof"
                  type="daterange"
                  align="right"
                  unlink-panels
                  value-format='timestamp'
                  range-separator="至"
                  start-placeholder="开始日期"
                  end-placeholder="结束日期"
                  @change="getList(1)"
                  popper-class="datetime"
                >
                </el-date-picker>
              </el-form-item>

              <!-- <el-form-item
                label="电箱名称"
                prop="region"
                class="dianxiang"
              >
                <el-select
                  v-model="form.region"
                  filterable
                  placeholder="全部"
                >
                  <el-option
                    v-for="(item,index) in options"
                    :label="'['+getString1(hexCharCodeToStr(item.nid))+'] ' + item.n_name + ' [' + item.n_local +']'"
                    :value="item.nid"
                    :key="index"
                  ></el-option>
                </el-select>
              </el-form-item> -->

              <!-- <el-button
                type="primary"
                @click="submitForm"
                style="height: 32px;"
              >查询</el-button> -->

            </el-form>

            <el-table
              :data="tableData"
              border
              height="240"
              style="width: 100%"
            >

              <el-table-column
                prop="num"
                show-overflow-tooltip
                label="排名"
                align="center"
                min-width="80"
              ></el-table-column>

              <el-table-column
                prop="name"
                show-overflow-tooltip
                label="电箱名称"
                align="center"
                min-width="80"
              ></el-table-column>

              <el-table-column
                prop="total"
                show-overflow-tooltip
                label="报警数目"
                align="center"
                min-width="80"
              ></el-table-column>

              <!-- <el-table-column
                prop="name"
                show-overflow-tooltip
                label="未处理数目"
                align="center"
                min-width="80"
              ></el-table-column> -->

              <el-table-column
                label="未确认数目"
                align="center"
                min-width="80"
              >
                <template slot-scope="scope">
                  <span style="margin-left: 10px">{{ scope.row.total -  scope.row.chuli - scope.row.chakan}}</span>
                </template>
              </el-table-column>

              <el-table-column
                prop="clbili"
                show-overflow-tooltip
                label="处理比"
                align="center"
                min-width="60"
              ></el-table-column>

            </el-table>

            <div id="echarts1"></div>
          </div>

          <!-- 报警类型 -->
          <div style="width:46%">
            <el-form
              ref="formalarmtype"
              :model="formalarmtype"
              class="demo-ruleForm typeform"
              label-width="80px"
            >
              <el-form-item
                label="选择时间"
                prop="p1"
              >
                <el-date-picker
                  v-model="formalarmtype.dateof"
                  type="daterange"
                  align="right"
                  unlink-panels
                  value-format='timestamp'
                  range-separator="至"
                  start-placeholder="开始日期"
                  end-placeholder="结束日期"
                  popper-class="datetime"
                  @change="getalarmtype"
                >
                </el-date-picker>
              </el-form-item>

              <el-form-item
                label="电箱名称"
                prop="region"
                class="dianxiang"
              >
                <el-select
                  v-model="formalarmtype.region"
                  filterable
                  clearable
                  placeholder="全部"
                  @change="getalarmtype"
                >
                  <el-option
                    v-for="(item,index) in options"
                    :label="'['+getString1(hexCharCodeToStr(item.nid))+'] ' + item.n_name + ' [' + item.n_local +']'"
                    :value="item.nid"
                    :key="index"
                  ></el-option>
                </el-select>
              </el-form-item>

              <!-- <el-button
                type="primary"
                @click="submitForm"
                style="height: 32px;"
              >查询</el-button> -->

            </el-form>

            <el-table
              :data="tableDatatype"
              border
              height="240"
              style="width: 100%"
            >

              <el-table-column
                prop="num"
                show-overflow-tooltip
                label="排名"
                align="center"
                min-width="80"
              ></el-table-column>

              <el-table-column
                prop="name"
                show-overflow-tooltip
                label="报警类型"
                align="center"
                min-width="80"
              ></el-table-column>

              <el-table-column
                prop="total"
                show-overflow-tooltip
                label="报警数目"
                align="center"
                min-width="80"
              ></el-table-column>

              <el-table-column
                label="未确认数目"
                align="center"
                min-width="80"
              >
                <template slot-scope="scope">
                  <span style="margin-left: 10px">{{ scope.row.total -  scope.row.chuli - scope.row.chakan}}</span>
                </template>
              </el-table-column>

              <el-table-column
                prop="clbili"
                show-overflow-tooltip
                label="处理比"
                align="center"
                min-width="60"
              ></el-table-column>

            </el-table>

            <div id="echarts2"></div>
          </div>

        </div>

        <!-- 报警分析 -->
        <div
          v-show="activeName=='second'?true:false"
          class="tablebox"
        >
          <!-- 月度报警信息 -->
          <div style="width:46%">
            <el-form
              ref="formmonth"
              :model="formmonth"
              class="demo-ruleForm"
              label-width="90px"
            >
              <el-form-item
                label="选择时间"
                prop="p1"
              >
                <el-date-picker
                  v-model="formmonth.dateof"
                  type="month"
                  align="right"
                  unlink-panels
                  value-format='yyMM'
                  popper-class="datetime"
                  @change="getmonth"
                >
                </el-date-picker>
              </el-form-item>

              <el-form-item
                label="电箱名称"
                prop="region"
                class="dianxiang"
              >
                <el-select
                  v-model="formmonth.region"
                  filterable
                  clearable
                  placeholder="全部"
                  @change="getmonth"
                >
                  <el-option
                    v-for="(item,index) in options"
                    :label="'['+getString1(hexCharCodeToStr(item.nid))+'] ' + item.n_name + ' [' + item.n_local +']'"
                    :value="item.nid"
                    :key="index"
                  ></el-option>
                </el-select>
              </el-form-item>

              <!-- <el-button
                type="primary"
                @click="submitForm"
              >查询</el-button> -->

            </el-form>
            <div id="echarts3"></div>
            <div id="echarts4"></div>
          </div>

          <!-- 年度报警信息 -->
          <div style="width:46%">
            <el-form
              ref="formyear"
              :model="formyear"
              class="demo-ruleForm"
              label-width="90px"
            >
              <el-form-item
                label="选择时间"
                prop="p1"
              >
                <el-date-picker
                  v-model="formyear.dateof"
                  type="year"
                  align="right"
                  unlink-panels
                  value-format='yyyy'
                  popper-class="datetime"
                  @change="getyear"
                >
                </el-date-picker>
              </el-form-item>

              <el-form-item
                label="电箱名称"
                prop="region"
                class="dianxiang"
              >
                <el-select
                  v-model="formyear.region"
                  filterable
                  clearable
                  placeholder="全部"
                  @change="getyear"
                >
                  <el-option
                    v-for="(item,index) in options"
                    :label="'['+getString1(hexCharCodeToStr(item.nid))+'] ' + item.n_name + ' [' + item.n_local +']'"
                    :value="item.nid"
                    :key="index"
                  ></el-option>
                </el-select>
              </el-form-item>

              <!-- <el-button
                type="primary"
                @click="submitForm"
              >查询</el-button> -->

            </el-form>
            <div id="echarts5"></div>
            <div id="echarts6"></div>
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
import $ from "jquery";
import copyright from "../../components/copyright";
export default {
  name: "alarmProcessingwy",
  components: {
    copyright
  },
  data() {
    return {
      activeName: 'first',

      // 电箱报警列表数据检索
      form: {
        region: "",
        pu: "",
        line: "",
        dateof: []
      },
      // 报警类型排名检索
      formalarmtype: {
        region: "",
        dateof: []
      },
      // 月度报警信息检索
      formmonth: {
        dateof: "",
        region: ""
      },
      // 年度报警信息检索
      formyear: {
        dateof: "",
        region: ""
      },
      // 获取所有设备
      options: [],

      // 电箱报警列表数据
      tableData: [],
      // 电箱排名x轴
      elename: [],
      // 电箱排名数据
      eledata: [],

      // 报警类型列表数据
      tableDatatype: [],
      // 报警类型x轴
      typename: [],
      // 报警类型排名数据
      typedata: [],

      // 月度报警信息统计数据
      Mdata: [],
      // 月度报警类型占比数据
      monthlydata: [],

      // 年度报警信息统计数据
      Ydata: [],
      // 年度报警类型占比数据
      yeardata: [],

      // 报警类型
      jingtype: [],
      // 线路信息
      linearry: [],
      // 日月年报警
      todayMonthYear: {},
    };
  },
  methods: {
    handleClick(tab, event) {
      if (this.activeName == 'second') {
        this.getmonth()
        this.getyear()
        // this.getStudent3()
        // this.getStudent4()
        // this.getStudent5()
        // this.getStudent6()
      }
    },

    // 初始化获取下拉数据 - 所有设备
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
        this.options = res.data.data;
        this.formyear.region = this.options[0].nid
        this.formalarmtype.region = this.options[0].nid
        this.formmonth.region = this.options[0].nid
        this.getalarmtype()
      });
    },

    // 获取日月年报警次数环比
    getTodayMonthYear() {
      let username = localStorage.getItem("username");
      let uid = localStorage.getItem(username + 'uid');
      let type = 'post'
      let url = this.urla + '/api3/EnergyData/getJingfenxi'
      let data = {
        data: {
          uid: uid, //必传 表示查询用户的日月年环比数据
          // uid: localStorage.getItem('uid'), //必传 表示查询用户的日月年环比数据
          nid: "", //可选 表示查询电箱的日月年环比数据
          mid: ""  //可选 表示查询线路的日月年环比数据
        }
      }
      this.myAjax(type, url, data, res => {
        this.todayMonthYear = res.data.data
      })
    },

    // 获取报警排名电箱排名列表
    getList(page) {
      let username = localStorage.getItem("username");
      let uid = localStorage.getItem(username + 'uid');
      let type = "post";
      let url = this.urlb + "/api3/EnergyData/hwAgentJingChoose";
      let data = {
        data: {
          uid: uid,
          // uid: localStorage.getItem("uid"),
          start: this.form.dateof[0] / 1000,  //必传，没有选择就选当天开始的时间
          sendt: this.form.dateof[1] / 1000  //必传，没有就选择比start大的时间
        }
      };

      this.myAjax(type, url, data, res => {
        // console.log(res)
        this.elename = []
        this.eledata = []
        this.tableData = res.data.data;
        for (let i = 0; i < this.tableData.length; i++) {
          this.tableData[i].num = i + 1
          this.elename.push(this.tableData[i].name)
          this.eledata.push(this.tableData[i].total)
        }
        this.getStudent1()
      })
    },
    // 获取报警类别排名列表
    getalarmtype(page) {
      if (this.formalarmtype.region != "") {
        let username = localStorage.getItem("username");
        let uid = localStorage.getItem(username + 'uid');
        let type = "post";
        let url = this.urlb + "/api3/EnergyData/hwUserJingChoose";
        let data = {
          data: {
            uid: uid,
            // uid: localStorage.getItem("uid"),
            start: this.formalarmtype.dateof[0] / 1000,  //必传，没有选择就选当天开始的时间
            sendt: this.formalarmtype.dateof[1] / 1000,   //必传，没有就选择比start大的时间
            nid: this.formalarmtype.region   //必传
          }
        };

        this.myAjax(type, url, data, res => {
          // console.log(res)
          this.typename = []
          this.typedata = []
          this.tableDatatype = res.data.data;
          for (let i = 0; i < this.tableDatatype.length; i++) {
            if (this.tableDatatype[i].name == "占位") {
              this.tableDatatype.splice(i, 2)
            }
            this.tableDatatype[i].num = i + 1
            this.typename.push(this.tableDatatype[i].name)
            this.typedata.push(this.tableDatatype[i].total)
          }
          this.getStudent2()
        })
      } else {
        this.$message({
          showClose: true,
          message: '请选择电箱',
          type: 'warning'
        });
      }

    },

    // 月度报警信息
    getmonth() {
      if (this.formmonth.region != "") {
        let username = localStorage.getItem("username");
        let uid = localStorage.getItem(username + 'uid');
        let type = "post";
        let url = this.urlb + "/api3/EnergyData/monthJingFenxi";
        let data = {
          data: {
            uid: uid,
            // uid: localStorage.getItem("uid"),
            nid: this.formmonth.region, //可选，无nid参数即为该用户名下所有电箱
            time: this.formmonth.dateof    //必传，time参数四位数，前两位数表示年份的后两位，后两位数表示月份
          }
        };

        this.myAjax(type, url, data, res => {
          // console.log(res)
          this.Mdata = res.data.data.day
          this.monthlydata = res.data.data.type
          this.getStudent3()
          this.getStudent4()

        })
      } else {
        this.$message({
          showClose: true,
          message: '请选择电箱',
          type: 'warning'
        });
      }
    },
    // 年度报警信息
    getyear() {
      if (this.formyear.region != "") {
        let username = localStorage.getItem("username");
        let uid = localStorage.getItem(username + 'uid');
        let type = "post";
        let url = this.urlb + "/api3/EnergyData/yearJingFenxi";
        let data = {
          data: {
            uid: uid,
            // uid: localStorage.getItem("uid"),
            nid: this.formyear.region, //可选，无nid参数即为该用户名下所有电箱
            time: this.formyear.dateof    //必传，time参数四位数，前两位数表示年份的后两位，后两位数表示月份
          }
        };

        this.myAjax(type, url, data, res => {
          // console.log(res)
          this.yeardata = res.data.data
          this.Ydata = res.data.month
          this.getStudent5()
          this.getStudent6()
        })
      } else {
        this.$message({
          showClose: true,
          message: '请选择电箱',
          type: 'warning'
        });
      }
    },

    // 获取年和月
    getmonthyear(tYear) {
      var myDate = new Date();
      var tYear = myDate.getFullYear();
      var tMonth = myDate.getMonth();

      var m = tMonth + 1;
      if (m.toString().length == 1) {
        m = "0" + m;
      }
      this.formyear.dateof = tYear.toString()
      this.formmonth.dateof = tYear.toString().substr(2, 2) + m
    },

    // 电箱排名
    getStudent1() {
      // console.log(Math.max.apply(null, this.listdianya))
      let _this = this;
      let student = _this.$echarts.init(document.getElementById("echarts1"));
      $(window).resize(function () {
        student.resize();
      });
      student.setOption({
        title: {
          text: '电箱排名',
          textStyle: { //主标题文本样式
            color: "#ccc",
            fontSize: "14"
          },
          x: "10",
          y: "10"
        },
        tooltip: {},
        xAxis: {
          type: 'category',
          data: this.elename,
          axisLine: {
            lineStyle: {
              color: '#ccc',
            }
          },
        },
        dataZoom: [{
          type: 'inside',
          start: 0,
          end: 100,

        }, {
          start: 0,
          end: 10,
          handleIcon: 'M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z',
          handleSize: '80%',
          textStyle: { color: "#ccc" },
          handleStyle: {
            color: '#fff',
            shadowBlur: 3,
            shadowColor: 'rgba(0, 0, 0, 0.6)',
            shadowOffsetX: 2,
            shadowOffsetY: 2
          }
        }],
        yAxis: {
          type: 'value',
          splitLine: {
            show: true,
            lineStyle: {
              color: ['#47d'],
              width: 1,
              type: 'solid'
            }
          },
          axisLine: {
            lineStyle: {
              color: '#ccc',
            },
          },
        },
        series: [{
          data: this.eledata,
          type: 'bar',
          showBackground: true,
          barWidth: 30,//柱图宽度
          backgroundStyle: {
            color: 'rgba(220, 220, 220, 0.8)'
          },
          itemStyle: {
            normal: {
              //这里是重点
              color: function (params) {
                //注意，如果颜色太少的话，后面颜色不会自动循环，最好多定义几个颜色
                var colorList = [
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                ];
                return colorList[params.dataIndex];
              },
              label: {
                show: true,
                position: "top",
                formatter: "{b}\n{c}",
                formatter: "{c}"
              }
            }
          },
        }]
      });
    },

    // 报警类别排名
    getStudent2() {
      // console.log(Math.max.apply(null, this.listdianya))
      let _this = this;
      let student = _this.$echarts.init(document.getElementById("echarts2"));
      $(window).resize(function () {
        student.resize();
      });
      student.setOption({
        title: {
          text: '报警类别排名',
          x: "10",
          y: "10",
          textStyle: { //主标题文本样式
            color: "#ccc",
            fontSize: "14"
          },
        },
        tooltip: {},
        xAxis: {
          type: 'category',
          //坐标轴斜着显示
          axisLabel: {
            interval: 0,
            rotate: 40
          },
          data: this.typename,
          axisLine: {
            lineStyle: {
              color: '#ccc',
            }
          },
        },
        yAxis: {
          type: 'value',
          splitLine: {
            show: true,
            lineStyle: {
              color: ['#47d'],
              width: 1,
              type: 'solid'
            }
          },
          axisLine: {
            lineStyle: {
              color: '#ccc',
            },
          },
        },
        series: [{
          data: this.typedata,
          type: 'bar',
          showBackground: true,
          barWidth: 30,//柱图宽度
          backgroundStyle: {
            color: 'rgba(220, 220, 220, 0.8)'
          },
          itemStyle: {
            normal: {
              //这里是重点
              color: function (params) {
                //注意，如果颜色太少的话，后面颜色不会自动循环，最好多定义几个颜色
                var colorList = [
                  "#c23531",
                  "#BC8F8F",
                  "#61a0a8",
                  "#d48265",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622",
                  "#91c7ae",
                  "#749f83",
                  "#ca8622"
                ];
                return colorList[params.dataIndex];
              },
              label: {
                show: true,
                position: "top",
                formatter: "{b}\n{c}",
                formatter: "{c}"
              }
            }
          },
        }]
      });
    },

    // 月度报警信息统计
    getStudent3() {
      // console.log(Math.max.apply(null, this.listdianya))
      let _this = this;
      setTimeout(() => {
        let student = _this.$echarts.init(document.getElementById("echarts3"));
        $(window).resize(function () {
          student.resize();
        });

        student.setOption({
          title: {
            text: '月度报警信息统计',
            textStyle: { //主标题文本样式
              color: "#ccc",
              fontSize: "14"
            },
            x: "10",
            y: "10"
          },
          xAxis: {
            type: 'category',
            data: ['1日', '2日', '3日', '4日', '5日', '6日', '7日', '8日', '9日', '10日', '11日', '12日', '13日', '14日', '15日', '16日', '17日', '18日', '19日', '20日', '21日', '22日', '23日', '24日', '25日', '26日', '27日', '28日', '29日', '30日', '31日'],
            axisLine: {
              lineStyle: {
                color: '#ccc',
              }
            },
          },
          dataZoom: [{
            type: 'inside',
            start: 0,
            end: 100,

          }, {
            start: 0,
            end: 10,
            handleIcon: 'M10.7,11.9v-1.3H9.3v1.3c-4.9,0.3-8.8,4.4-8.8,9.4c0,5,3.9,9.1,8.8,9.4v1.3h1.3v-1.3c4.9-0.3,8.8-4.4,8.8-9.4C19.5,16.3,15.6,12.2,10.7,11.9z M13.3,24.4H6.7V23h6.6V24.4z M13.3,19.6H6.7v-1.4h6.6V19.6z',
            handleSize: '80%',
            textStyle: { color: "#ccc" },
            handleStyle: {
              color: '#fff',
              shadowBlur: 3,
              shadowColor: 'rgba(0, 0, 0, 0.6)',
              shadowOffsetX: 2,
              shadowOffsetY: 2
            }
          }],
          yAxis: {
            type: 'value',
            splitLine: {
              show: true,
              lineStyle: {
                color: ['#47d'],
                width: 1,
                type: 'solid'
              }
            },
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
          series: [{
            data: _this.Mdata,
            type: 'bar',
            showBackground: true,
            barWidth: 30,//柱图宽度
            backgroundStyle: {
              color: 'rgba(220, 220, 220, 0.8)'
            },
            itemStyle: {
              normal: {
                //这里是重点
                color: function (params) {
                  //注意，如果颜色太少的话，后面颜色不会自动循环，最好多定义几个颜色
                  var colorList = [
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                  ];
                  return colorList[params.dataIndex];
                },
                label: {
                  show: true,
                  position: "top",
                  formatter: "{b}\n{c}",
                  formatter: "{c}"
                }
              }
            },
          }]
        });
      }, 0);


    },
    // 月度报警类型占比
    getStudent4() {
      let _this = this;
      setTimeout(() => {
        let student = _this.$echarts.init(document.getElementById("echarts4"));
        $(window).resize(function () {
          student.resize();
        });
        student.setOption({
          title: {
            text: "月度报警类型占比",
            textStyle: { //主标题文本样式
              color: "#ccc",
              fontSize: "14"
            },
            x: "10",
            y: "10"
          },
          tooltip: {
            trigger: "item",
            formatter: "{a} <br/>{b}: {c} ({d}%)"
          },
          legend: {
            orient: "vertical",
            right: -10,
            top: 80,
            data: [
              "欠压告警",
              "过压预警",
              "过压报警",
              "电流告警",
              "电流预警",
              "电流报警",
              "漏电流报警",
              "温度报警",
              "分机报警"
            ],
            textStyle: {
              fontSize: 12,
              color: '#ccc'
            }
          },
          series: [
            {
              name: "",
              type: "pie",
              radius: ["50%", "70%"],
              avoidLabelOverlap: false,
              label: {
                show: false,
                position: "center"
              },
              emphasis: {
                label: {
                  show: true,
                  fontSize: "30",
                  fontWeight: "bold"
                }
              },
              labelLine: {
                show: false
              },
              data: [
                { value: this.monthlydata[0], name: "欠压告警" },
                { value: this.monthlydata[1], name: "过压预警" },
                { value: this.monthlydata[2], name: "过压报警" },
                { value: this.monthlydata[3], name: "电流告警" },
                { value: this.monthlydata[4], name: "电流预警" },
                { value: this.monthlydata[5], name: "电流报警" },
                { value: this.monthlydata[6], name: "漏电流报警" },
                { value: this.monthlydata[7], name: "温度报警" },
                { value: this.monthlydata[8], name: "分机报警" }
              ]
            }
          ]
        });
      }, 0);

    },

    // 年度报警信息统计
    getStudent5() {
      // console.log(Math.max.apply(null, this.listdianya))
      let _this = this;
      setTimeout(() => {
        let student = _this.$echarts.init(document.getElementById("echarts5"));
        $(window).resize(function () {
          student.resize();
        });

        student.setOption({
          title: {
            text: '年度报警信息统计',
            textStyle: { //主标题文本样式
              color: "#ccc",
              fontSize: "14"
            },
            x: "10",
            y: "10"
          },
          xAxis: {
            type: 'category',
            data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月'],
            axisLine: {
              lineStyle: {
                color: '#ccc',
              }
            },
          },
          yAxis: {
            type: 'value',
            splitLine: {
              show: true,
              lineStyle: {
                color: ['#47d'],
                width: 1,
                type: 'solid'
              }
            },
            axisLine: {
              lineStyle: {
                color: '#ccc',
              },
            },
          },
          series: [{
            data: _this.Ydata,
            type: 'bar',
            showBackground: true,
            barWidth: 30,//柱图宽度
            backgroundStyle: {
              color: 'rgba(220, 220, 220, 0.8)'
            },
            itemStyle: {
              normal: {
                //这里是重点
                color: function (params) {
                  //注意，如果颜色太少的话，后面颜色不会自动循环，最好多定义几个颜色
                  var colorList = [
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#c23531",
                    "#BC8F8F",
                    "#61a0a8",
                    "#d48265",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                    "#91c7ae",
                    "#749f83",
                    "#ca8622",
                  ];
                  return colorList[params.dataIndex];
                },
                label: {
                  show: true,
                  position: "top",
                  formatter: "{b}\n{c}",
                  formatter: "{c}"
                }
              }
            },
          }]
        });
      })

    },
    // 年度报警类型占比
    getStudent6() {
      let _this = this;
      setTimeout(() => {
        let student = _this.$echarts.init(document.getElementById("echarts6"));
        $(window).resize(function () {
          student.resize();
        });
        student.setOption({
          title: {
            text: "年度报警类型占比",
            textStyle: { //主标题文本样式
              color: "#ccc",
              fontSize: "14"
            },
            x: "10",
            y: "10"
          },
          tooltip: {
            trigger: "item",
            formatter: "{a} <br/>{b}: {c} ({d}%)"
          },
          legend: {
            orient: "vertical",
            right: -10,
            top: 80,
            data: [
              "欠压告警",
              "过压预警",
              "过压报警",
              "电流告警",
              "电流预警",
              "电流报警",
              "漏电流报警",
              "温度报警",
              "分机报警"
            ],
            textStyle: {
              fontSize: 12,
              color: '#ccc'
            }
          },
          series: [
            {
              name: "",
              type: "pie",
              radius: ["50%", "70%"],
              avoidLabelOverlap: false,
              label: {
                show: false,
                position: "center"
              },
              emphasis: {
                label: {
                  show: true,
                  fontSize: "30",
                  fontWeight: "bold"
                }
              },
              labelLine: {
                show: false
              },
              data: [
                { value: this.yeardata[0], name: "欠压告警" },
                { value: this.yeardata[1], name: "过压预警" },
                { value: this.yeardata[2], name: "过压报警" },
                { value: this.yeardata[3], name: "电流告警" },
                { value: this.yeardata[4], name: "电流预警" },
                { value: this.yeardata[5], name: "电流报警" },
                { value: this.yeardata[6], name: "漏电流报警" },
                { value: this.yeardata[7], name: "温度报警" },
                { value: this.yeardata[8], name: "分机报警" }
              ]
            }
          ]
        });
      }, 0);

    },

    // 查询
    submitForm() {
      this.getList(1);
    },

    // 获取当前日期和前一天
    setdate() {
      var timestamp = (new Date()).getTime();

      // console.log(timestamp);//打印当前时间戳
      this.form.dateof.push(timestamp - 24 * 60 * 60 * 1000)
      this.form.dateof.push(timestamp)
      // console.log(timestamp - 24 * 60 * 60 * 1000);
      this.formalarmtype.dateof.push(timestamp - 24 * 60 * 60 * 1000)
      this.formalarmtype.dateof.push(timestamp)
    }

  },
  mounted() {
    this.getmonthyear()
  },
  created() {
    this.getTodayMonthYear()
    this.getOptions();
    this.setdate()
    this.getList(1);
    // this.getalarmtype(1)
  }
};
</script>
  
<style lang="less" scoped>
.alarmProcessingwy .el-header .el-form /deep/ .el-radio-button__inner {
  padding: 7px 10px !important;
}
.alarmProcessingwy {
  height: 100%;
  .el-header {
    .el-form {
      text-align: left;
      margin-top: 10px;
      .el-form-item {
        float: left;
      }
      .el-date-editor.el-input,
      .el-date-editor.el-input__inner {
        // width: 150px;
        border: 1px solid #47d;
        background: transparent;
      }
      .el-date-editor /deep/.el-range-separator {
        color: #ccc;
      }
      .el-date-editor /deep/.el-range-input {
        background: transparent;
      }

      .el-button {
        margin-top: 3px;
        padding: 9px 20px;
        margin-left: 20px;
      }
      .dianxiang {
        width: 360px;
        .el-select {
          width: 100%;
        }
      }
      .el-date-editor /deep/ .el-range__icon {
        line-height: 26px !important;
      }
    }
  }
  .el-header /deep/ .el-tabs__item {
    color: #ccc;
  }
  .el-header /deep/ .is-active {
    color: teal;
  }

  .el-main {
    color: #333;
    text-align: center;
    padding: 0;
    height: 100%;
    .el-form {
      text-align: left;
      margin-top: 10px;
      display: flex;
      // .el-form-item {
      //   float: left;
      // }
      .el-date-editor/deep/.el-input,
      .el-date-editor/deep/.el-input__inner {
        color: #ccc;
        border: 1px solid #47d;
        background: transparent;
      }
      .el-form-item/deep/.el-form-item__label,
      .el-date-editor /deep/.el-range-separator {
        color: #ccc;
      }
      .el-date-editor /deep/.el-range-input {
        color: #ccc;
        background: transparent;
      }

      .el-button {
        margin-top: 3px;
        padding: 9px 20px;
        margin-left: 20px;
      }
      .dianxiang {
        width: 275px;
        .el-select {
          width: 100%;
        }
        .el-select /deep/ .el-input__inner {
          color: #ccc;
          border: 1px solid #47d;
          background: transparent;
        }
      }
      .el-date-editor /deep/ .el-range__icon {
        line-height: 26px !important;
      }
    }
    .el-table /deep/ th,
    .el-table /deep/ td {
      padding: 8px 0;
    }
    .onebox {
      .el-date-editor {
        width: 260px;
        border: 1px solid #47d;
        background: transparent;
      }
    }

    .infobar {
      height: 60px;
      color: #ccc;
      display: flex;
      justify-content: space-around;
      padding: 9px 0;
      background: rgba(0, 0, 0, 0.4);
      .item {
        display: flex;
        justify-content: space-around;
        .numbox {
          margin: 0 10px;
          .numtext {
            margin-bottom: 12px;
          }
          .num {
            font-size: 22px;
            font-weight: bold;
          }
        }
      }
    }
    .tablebox {
      // margin-top: 20px;
      display: flex;
      justify-content: space-around;
    }
    #echarts1,
    #echarts2,
    #echarts3,
    #echarts4,
    #echarts5,
    #echarts6 {
      width: 100%;
      height: 300px;
      margin-top: 20px;
      background: rgba(0, 0, 0, 0.15);
      border: 1px solid #47d;
    }

    .oo2 {
      font-size: 20px;
      vertical-align: middle;
      margin: -2px 5px 0 5px;
      color: #f56c6c;
    }
    .oo1 {
      font-size: 20px;
      vertical-align: middle;
      margin: -2px 5px 0 5px;
      color: #008080;
    }
  }
  .el-footer {
  }
}
</style>
  