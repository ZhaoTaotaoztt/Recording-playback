
<template>
  <div class="record">
    <div id="table">
      <table class="table">
        <tr>
          <th>id</th>
          <th>
            <el-button
              type="warning"
              icon="el-icon-circle-plus"
              circle
              @click="Showdialog"
            ></el-button>

            <!-- 删除按钮在这 -->
            <el-button
              type="warning"
              icon="el-icon-delete"
              circle
              @click="Deletrecord()"
            ></el-button>
            <!-- 删除按钮在这 -->
          </th>
          <th>RecordChannelIndex</th>
          <th>Filesize(Byte)</th>
          <th>BitNum</th>
          <th>SampleRate(Hz)</th>
          <th>RecordBandWidth(Hz)</th>
          <th>RecordFrequency(Hz)</th>
          <th>RecordXRgain</th>
          <th>Describe</th>
        </tr>
        <tr v-for="(item, index) in record" :key="index">
          <!-- 复选框在这 -->
          <td>{{ item.id }}</td>
          <td>
            <span class="add">Add Parameters</span>&nbsp;
            <input
              type="checkbox"
              @change="Checkedrecord($event, item)"
              class="select"
              ref="checkbox"
            />
          </td>
          <!-- 复选框在这 -->

          <td>{{ item.RecordChannelIndex }}</td>
          <td>{{ item.FileSize }}</td>
          <td><input type="text" class="text" v-model="item.BitNumber"> </td>
          <td><input type="text" class="text" v-model="item.SampleRate"></td>
          <td><input type="text" class="text" v-model="item.RecordBandwidth"></td>
          <td><input type="text" class="text" v-model="item.RecordRXFrequency"></td>
          <td><input type="text" class="text" v-model="item.RecordRXGain"></td>
          <td><input type="text" class="text" v-model="item.Describe"></td>
        </tr>
      </table>
    </div>

    <p>
      <el-button id="btn" @click="StartRecord" v-preventReClick="30000"
        >Start Record</el-button
      >
      <el-button id="btn" @click="StartSynRecord" v-preventReClick="30000"
        >Start SynRecord</el-button
      >
    </p>

    <!-- 嵌套的表单 -->
    <el-dialog
      title="新建录制配置"
      id="dialogrecord"
      :visible.sync="dialogrecord"
      :before-close="close"
      :modal="modal"
    >
      <el-form>
        <el-form-item label="ID" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.id"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="RecordChannelIndex" :label-width="formLabelWidth">
          <el-select v-model="recordform.RecordChannelIndex">
            <el-option label="0" value="0"></el-option>
            <el-option label="1" value="1"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="Filesize(Byte)" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.FileSize"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="BitNum" :label-width="formLabelWidth">
          <el-select v-model="recordform.BitNumber">
            <el-option label="2" value="2"></el-option>
            <el-option label="4" value="4"></el-option>
            <el-option label="8" value="8"></el-option>
            <el-option label="16" value="16"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="SampleRate(Byte)" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.SampleRate"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="RecordBandWidth(Hz)" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.RecordBandwidth"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="RecordFrequency(Hz)" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.RecordRXFrequency"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="RecordXRgain" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.RecordRXGain"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="Describe" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.Describe"
            autocomplete="off"
            type="textarea"
            maxlength="256"
            placeholder="Can be empty, with a maximum length of 256"
          ></el-input>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogrecord = false">取 消</el-button>
        <el-button type="primary" @click="addRecordData(recordform.id)"
          >确 定</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      record: [
        {
          RecordChannelIndex: 0,
          FileSize: 1000000000,
          BitNumber: 2,
          SampleRate: 122880000,
          RecordBandwidth: 10000000,
          RecordRXFrequency: 1575420000,
          RecordRXGain: 40,
          id: 2,
        },
        {
          RecordChannelIndex: 1,
          FileSize: 1000000000,
          BitNumber: 2,
          SampleRate: 122880000,
          RecordBandwidth: 10000000,
          RecordRXFrequency: 1575420000,
          RecordRXGain: 40,
          id: 3,
        },
        {
          RecordChannelIndex: 1,
          FileSize: 1000000000,
          BitNumber: 8,
          SampleRate: 122880000,
          RecordBandwidth: 10000000,
          RecordRXFrequency: 1575420000,
          RecordRXGain: 40,
          id: 4,
        },
        {
          RecordChannelIndex: 0,
          FileSize: 1000000000,
          BitNumber: 8,
          SampleRate: 122880000,
          RecordBandwidth: 10000000,
          RecordRXFrequency: 1575420000,
          RecordRXGain: 40,
          id: 1,
        },
      ],
      modal: false, //不要蒙层
      ChannelIndex: [], //板卡
      RemainHarddiskSize: 0, //用来存储磁盘大小
      IndexList: [], //用来控制只可以选中两个index不一样的复选框
      Availablespace: "",

      removeData: [], //需要删除的数据
      RecordData: [], //需要录制的数据

      recordboolen: false, //用来石佛允许点击按钮的布尔值

      isclick: true, //用来判断提示不要频繁点击的布尔值

      dialogrecord: false, //对话框是否显示

      recordform: {
        //配置录制的数据的对话框所绑定的数据
        id: 1,
        RecordChannelIndex: 0,
        FileSize: 1000000000,
        BitNumber: 2,
        SampleRate: 122880000,
        RecordBandwidth: 10000000,
        RecordRXFrequency: 1575420000,
        RecordRXGain: 40,
        Describe:"",
      },

      formLabelWidth: "165px", //控制对话框的长度
    };
  },
  created() {
    this.Availablespace = this.$store.state.Availablespace;
    this.ChannelIndex = this.$store.state.ChannelIndex;
    this.record = JSON.parse(window.localStorage.getItem("recordData")); //获取localStorage本地存储的数据
    this.record = this.record.sort((a, b) => a.id - b.id); //进行排序
  },
  methods: {
    getRemainHarddiskSize() {
      // //socket请求----
      // var ws = new WebSocket("ws://192.168.1.203:9001");
      // ws.onopen = function (e) {
      //   ws.send(
      //     JSON.stringify({
      //       cmd: {
      //         APIName: "QueryFileInfo",
      //         RemainHarddiskSize: -1,
      //         FileNumber: -1,
      //         FileInformations: [
      //           {
      //             FileName: "NA",
      //             FileSize: 0,
      //           },
      //         ],
      //       },
      //     })
      //   );
      // };
      // var that = this;
      // ws.onmessage = function (e) {
      //   if (JSON.parse(e.data).APIName == "GenericErr") {
      //     that.$message.error("通用错误!");
      //   } else {
      //     that.RemainHarddiskSize = JSON.parse(e.data).cmd.RemainHarddiskSize;
      //     // console.log(that.RemainHarddiskSize);
      //   }
      //   //关闭socket连接
      //   ws.close();
      //   ws.onclose = function (e) {
      //     console.log(e);
      //   };
      // };
      // //socket请求----
    },
    Showdialog() {
      this.dialogrecord = true;
      this.ChannelIndex = this.$store.state.ChannelIndex;
      console.log(this.ChannelIndex);
    },
    addRecordData(id) {
      //添加新的数据到record
      this.dialogrecord = false;
      this.recordform = {
        id: parseInt(this.recordform.id),
        RecordChannelIndex: parseInt(this.recordform.RecordChannelIndex),
        FileSize: parseInt(this.recordform.FileSize),
        BitNumber: parseInt(this.recordform.BitNumber),
        SampleRate: parseInt(this.recordform.SampleRate),
        RecordBandwidth: parseInt(this.recordform.RecordBandwidth),
        RecordRXFrequency: parseInt(this.recordform.RecordRXFrequency),
        RecordRXGain: parseInt(this.recordform.RecordRXGain),
        Describe: this.recordform.Describe,
      };
      if (this.record.filter((item) => item["id"] === id).length !== 0) {
        this.$message({
          message: "id 已经存在，请重新输入！",
          type: "warning",
        });
      } else {
        this.record.push(this.recordform);
        this.recordform = {
          id: this.recordform.id,
          RecordChannelIndex: this.recordform.RecordChannelIndex,
          FileSize: this.recordform.FileSize,
          BitNumber: this.recordform.BitNumber,
          SampleRate: this.recordform.SampleRate,
          RecordBandwidth: this.recordform.RecordBandwidth,
          RecordRXFrequency: this.recordform.RecordRXFrequency,
          RecordRXGain: this.recordform.RecordRXGain,
          Describe: this.recordform.Describe,
        };

        //存数据
        window.localStorage.setItem("recordData", JSON.stringify(this.record));

        //取数据
        this.record = JSON.parse(window.localStorage.getItem("recordData"));

        this.record = this.record.sort((a, b) => a.id - b.id); //进行排序
      }
    },
    Checkedrecord(e, item) {
      let index = item.RecordChannelIndex;
      if (e.target.checked == true) {
        if (this.IndexList.indexOf(index) == -1) {
          this.IndexList.unshift(index);
          this.recordboolen = true;

          console.log(this.record[index]);

          //需要删除的数据
          this.removeData.push(item.id);

          //需要录制的数据
          this.RecordData.push({
            FileName: "NA",
            FileSize: parseInt(item.FileSize),
            BitNumber: parseInt(item.BitNumber),
            SampleRate: parseInt(item.SampleRate),
            RecordBandwidth: parseInt(item.RecordBandwidth),
            RecordRXFrequency: parseInt(item.RecordRXFrequency),
            RecordRXGain: parseInt(item.RecordRXGain),
            RecordChannelIndex: parseInt(item.RecordChannelIndex),
          });
          console.log(this.RecordData);
        } else {
          e.target.checked = false;
          this.recordboolen = false;
        }
      } else {
        this.IndexList.splice(this.IndexList.indexOf(index), 1);
        this.RecordData.splice(this.IndexList.indexOf(index), 1);
        this.removeData.splice(this.IndexList.indexOf(index), 1);
        this.recordboolen = false;
        console.log(this.RecordData);
      }
    },
    Deletrecord() {
      var arr = this.record;
      var ids = this.removeData;
      function bantchDelete(taskList, deleteTaskIds) {
        for (let i = 0; i < taskList.length; ) {
          let task = taskList[i];
          //根据id删除
          if (deleteTaskIds.indexOf(task.id) !== -1) {
            taskList.splice(i, 1);
            continue;
          }
          i++;
        }
        return taskList;
      }

      if (this.recordboolen == true) {
        this.$confirm("确定要删除这条信息吗？")
          .then((_) => {
            bantchDelete(arr, ids);
            // console.log(bantchDelete(arr, ids));
            this.recordboolen = false;
            this.RecordData = [];
            console.log(arr);
            window.localStorage.setItem("recordData", JSON.stringify(arr)); //存储覆盖
          })
          .catch((err) => {
            console.log(err);
          });
      } else {
        return;
      }
      //取消所有的复选框的勾选
      this.$refs.checkbox.map(function (item) {
        item.checked = false;
      });
      this.IndexList = [];
      //取消所有的复选框的勾选
    },
    close() {
      this.dialogrecord = false;
    },
    StartRecord() {
      if (this.recordboolen == true) {
        if (parseInt(this.Availablespace) < 5220000000) {
          this.$message({
            message: "磁盘空间不足！！！",
            type: "warning",
          });
          this.RecordData = [];
        } else {
          var data = this.RecordData;
          var item;
          console.log(data);
          //socket请求----
          var ws = new WebSocket("ws://192.168.1.203:9001");

          ws.onopen = function (e) {
            for (var i = 0; i < data.length; i++) {
              item = data[i];
              console.log(item);
              console.log(
                JSON.stringify({
                  cmd: {
                    APIName: "AddFileInfo",
                    FileInformations: [item],
                  },
                })
              );
              ws.send(
                JSON.stringify({
                  cmd: {
                    APIName: "AddFileInfo",
                    FileInformations: [item],
                  },
                })
              );
            }
          };
          var that = this;
          ws.onmessage = function (e) {
            if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
              that.$message.error("通用错误!");
            } else {
              console.log(JSON.parse(e.data).cmd.ResultCode);
              var staut = parseInt(JSON.parse(e.data).cmd.ResultCode);
              // console.log(this.boardinfo);
              switch (staut) {
                case 0:
                  that.$message({
                    message: "成功",
                    type: "success",
                  });
                  break;
                case 1:
                  that.$message.error("录制失败!");
                  break;
                case 2:
                  that.$message({
                    message: "板卡通道ID已经被使用!",
                    type: "warning",
                  });
                  break;
              }
            }

            that.RecordData = [];

            //关闭TCP连接
            ws.close();
            ws.onclose = function (e) {
              console.log(e);
            };
          };
        }
        //socket请求----

        //取消所有的复选框的勾选
        this.$refs.checkbox.map(function (item) {
          item.checked = false;
        });
        this.IndexList = [];
        //取消所有的复选框的勾选

        //提示不要频繁点击
        if (this.isclick) {
          this.isclick = false;
          setTimeout(() => {
            this.isclick = true;
          }, 4000);
        } else {
          this.$message({
            message: "请不要频繁点击！",
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.recordboolen = false;
      } else {
        return;
      }
    },
    StartSynRecord() {
      if (this.recordboolen == true) {
        if (parseInt(this.Availablespace) < 5220000000) {
          this.$message({
            message: "磁盘空间不足！！！",
            type: "warning",
          });
          this.RecordData = [];
        } else {
          var data;
          setTimeout(() => {
            data = this.RecordData;
            console.log(data);
            console.log(this.RecordData);
            console.log(
              JSON.stringify({
                cmd: {
                  APIName: "AddFileInfo",
                  FileInformations: data,
                },
              })
            );

            //socket请求----
            var ws = new WebSocket("ws://192.168.1.203:9001");
            ws.onopen = function (e) {
              ws.send(
                JSON.stringify({
                  cmd: {
                    APIName: "AddFileInfo",
                    FileInformations: data,
                  },
                })
              );
            };
            var that = this;
            ws.onmessage = function (e) {
              if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                that.$message.error("通用错误!");
              } else {
                console.log(JSON.parse(e.data).cmd.ResultCode);
                var staut = parseInt(JSON.parse(e.data).cmd.ResultCode);
                // console.log(this.boardinfo);
                switch (staut) {
                  case 0:
                    that.$message({
                      message: "成功",
                      type: "success",
                    });
                    break;
                  case 1:
                    that.$message.error("录制失败!");
                    break;
                  case 2:
                    that.$message({
                      message: "板卡通道ID已经被使用!",
                      type: "warning",
                    });
                    break;
                }
              }

              that.RecordData = [];

              //关闭TCP连接
              ws.close();
              ws.onclose = function (e) {
                console.log(e);
              };
            };
          }, 800);
        }
        //socket请求----

        //取消所有的复选框的勾选
        this.$refs.checkbox.map(function (item) {
          item.checked = false;
        });
        this.IndexList = [];
        //取消所有的复选框的勾选

        //提示不要频繁点击
        if (this.isclick) {
          this.isclick = false;
          setTimeout(() => {
            this.isclick = true;
          }, 4000);
        } else {
          this.$message({
            message: "请不要频繁点击！",
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.recordboolen = false;

        // setTimeout(() => {
        //   this.RecordData = [];
        // }, 500);
        // console.log(this.RecordData);
      } else {
        return;
      }
    },
  },
};
</script>


<style scoped >
.record {
  list-style-type: none !important ;
}
#table {
  width: 100%;
  height: auto;
  overflow-x: scroll;
}
.table {
  width:110%;
  height: auto;
  /* background-color: pink; */
  margin: 0rem auto;
}
.table th {
  height: 5rem;
  font-size: 1.3rem;
  border-bottom: 1px solid gainsboro;
}
.table td {
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  border-bottom: 1px solid gainsboro;
  color: rgb(151, 151, 146);
}
.table td input{
  outline-color: aliceblue;
  border: none;
  color: gray;
  text-align: center;
  /* width: 100%;
  height: 100%; */
}
.table td .text{
  outline-color: aliceblue;
  border: none;
  /* color: gray; */
  text-align: center;
  width: 100%;
  height: 50%;
}
.record {
  position: relative;
}
.record >>> table .el-checkbox__input.is-checked .el-checkbox__inner {
  width: 16px;
  height: 16px;
  background-color: rgb(245, 154, 35);
  background-size: cover;
  border: rgb(245, 154, 35) 3px solid;
}
.record >>> table .el-checkbox__inner {
  width: 16px;
  height: 16px;
  border: rgb(245, 154, 35) 3px solid;
}
.record >>> p .el-button {
  background-color: rgb(245, 154, 35);
  color: white;
  font-size: 1rem;
  margin-left: 50px;
  width: 250px;
}
.record >>> .add {
  color: rgb(37, 141, 222);
}
.el-input {
  margin-left: -5%;
  width: 50%;
}
.record >>> .el-input--suffix .el-input__inner {
  margin-left: 35% !important;
}
.record >>> .el-dialog {
  width: 40%;
}
.record >>> .el-select-dropdown {
  margin-left: 7%;
}

@media screen and (min-width: 600px) and (max-width: 1683px) {
  .record >>> .el-select > .el-input {
    margin-left: -19% !important;
  }
}
/* @media screen and (min-width: 600px) and (max-width:1145px) {
.record >>> .el-select>.el-input{
  margin-left:-19% !important;
}
} */
</style>