<template>
  <div id="app">
    <!-- <keep-alive> -->
    <!-- <video
      src="../../public/static/images/homeBJ.mp4"
      autoplay="autoplay"
      loop="loop"
      muted="muted"
      height="100%"
      width="100%"
      style="opacity: .4;"
    ></video> -->
    <audio
      autoplay="autoplay"
      id="auto"
      src=""
    ></audio>
    <router-view />
    <!-- </keep-alive> -->
  </div>
</template>

  <script>
import $ from "jquery"
export default {

  // 监听报警信息
  watch: {
    count(newCount, oldCount) {
      // console.log(newCount, oldCount)
      if (newCount.type == "jing") {
        this.playSound('http://data.huiyi8.com/2017/gha/03/17/1702.mp3')

        this.$notify.warning({
          title:
            newCount.desc +
            "-" +
            new Date(newCount.time * 1000).Format("yy-MM-dd hh:mm:ss"),
          type: "warning",
          duration: 0,
          message:
            ("i",
              { style: "color: teal" },
              "[" +
              this.getString1(this.hexCharCodeToStr(newCount.nid)) +
              newCount.mzname +
              "]" +
              " - 线路" +
              newCount.aa +
              " : " +
              newCount.beizhu)
        });
      }
    }
  },

  methods: {
    // 播放提示音
    playSound(src) {
      // console.log(src)
      var auto = $("#auto");
      auto.attr("src", src);
    },
  },

  computed: {
    count() {
      return this.$store.state.socketData;
    }
  },

  created() {
    this.socket();
  },
};
</script>
<style lang="less">
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  #app {
    font-family: "Avenir", Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    height: 100%;
  }
}
.el-dialog__header {
  line-height: 0;
}
.el-table {
  .el-button {
    padding: 0;
  }
}
video {
  position: fixed;
  top: 0;
  left: 0;
  object-fit: fill;
}
.el-main {
  .el-header {
    background: #22366d;
    // margin: 10px 0;
    margin: 0 0 10px 0;
    .el-form-item /deep/ .el-form-item__label {
      color: #ccc !important;
    }
    .el-input/deep/.el-input__inner {
      background: transparent;
      border: 1px solid #47d;
    }
    .el-input--suffix .el-input__inner {
      color: #ccc;
      border: 1px solid #47d;
      background: transparent;
    }

    .el-date-editor.el-input,
    .el-date-editor.el-input__inner {
      // width: 150px;
      // border: 1px solid #47d;
      background: transparent;
    }
    .el-date-editor /deep/.el-range-input {
      color: #ccc;
      background: transparent;
    }
  }
  .el-dialog {
    background: #27428e !important;
    .el-dialog__title {
      color: #ccc;
    }
    .el-dialog__body {
      color: #ccc;
    }
  }
}
.el-input /deep/ .el-input__inner,
.el-select /deep/ .el-input__inner,
.el-textarea /deep/ .el-textarea__inner {
  color: #ccc;
  background: #22366d;
  border: 1px solid #47d;
}
.el-picker-panel /deep/ .el-picker-panel__footer {
  background: #27428e;
}

.el-radio__label {
  color: #ccc;
}

.el-picker-panel__content /deep/.el-month-table .cell,
.el-picker-panel__content /deep/.el-year-table .cell {
  color: #ccc;
}

.el-picker-panel,
.el-popper /deep/ .el-select-dropdown__list {
  background: #27428e !important;
  .el-select-dropdown__item,
  .el-picker-panel__icon-btn {
    color: #ccc;
  }
}
.el-select-dropdown__item.hover,
.el-select-dropdown__item:hover {
  background-color: #47d !important;
}

.datetime {
  background-color: #27428e !important;
}

.el-picker-panel__body {
  color: #ccc;
  .el-date-table th {
    color: #ccc;
  }
  .el-date-picker__header {
    button,
    span {
      color: #ccc;
    }
  }
  .el-date-table td.disabled div {
    background: #5b93ff;
  }
}
.el-picker-panel__sidebar {
  background: transparent !important;
  .el-picker-panel__shortcut {
    color: #ccc;
  }
}

.el-scrollbar {
  color: #ccc;
}

.el-table td,
.el-table th {
  font-size: 12px !important;
}
.el-table th {
  background: transparent !important;
  background-color: rgba(0, 0, 0, 0.3) !important;
  color: #ccc;
}
.el-input__inner {
  height: 34px !important;
  line-height: 34px !important;
}
.el-select-dropdown__item {
  text-align: left;
}
.el-footer {
  // 分页设置
  height: 34px !important;
  .el-pagination__total,
  .el-pagination__jump,
  .number {
    color: #ccc;
  }
  .btn-prev,
  .number,
  .btn-next {
    color: #ccc !important;
    background: transparent !important;
  }
}
.el-footer /deep/ .el-pagination__editor.el-input .el-input__inner {
  color: #ccc;
  height: 28px !important;
  background: transparent;
}
</style>
