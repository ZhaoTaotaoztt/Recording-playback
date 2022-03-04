
<template>
  <div class="record">
    <div id="table">
      <table>
        <tr>
          <th>id</th>
          <th>
            <el-button
              type="warning"
              icon="el-icon-circle-plus"
              circle
              @click="dialogrecord = true"
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
          <th>BoardIndex</th>
          <th>Filesize(Byte)</th>
          <th>BitNum</th>
          <th>SampleRate(Hz)</th>
          <th>RecordBandWidth(Hz)</th>
          <th>RecordFrequency(Hz)</th>
          <th>RecordXRgain(Hz)</th>
        </tr>
        <tr v-for="item in record" :key="item.id">
          <!-- 复选框在这 -->
          <td>{{ item.id }}</td>
          <td>
            Add Parameters&nbsp;<el-checkbox
              v-model="item.checked"
              @change="Checkedrecord(item)"
              class="select"
            ></el-checkbox>
          </td>
          <!-- 复选框在这 -->

          <td>{{ item.ChanneIndex }}</td>
          <td>{{ item.FileSize }}</td>
          <td>{{ item.BitNumber }}</td>
          <td>{{ item.SampleRate }}</td>
          <td>{{ item.RecordBandwidth }}</td>
          <td>{{ item.RecordRXFrequency }}</td>
          <td>{{ item.RecordRXGain }}</td>
        </tr>
      </table>
    </div>

    <p>
      <el-button id="btn" @click="StartRecord">Start Record</el-button>
      <el-button id="btn" @click="StartSynRecord">Start SynRecord</el-button>
    </p>

    <!-- 嵌套的表单 -->
    <el-dialog
      title="新建录制配置"
      id="dialogrecord"
      :visible="dialogrecord"
      :before-close="close"
    >
      <el-form>
        <el-form-item label="ID" :label-width="formLabelWidth">
          <el-input v-model="recordform.id" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="ChanneIndex" :label-width="formLabelWidth">
          <el-select v-model="recordform.ChanneIndex">
            <el-option label="0" value="0"></el-option>
            <el-option label="1" value="1"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="Filesize" :label-width="formLabelWidth">
          <el-input v-model="recordform.FileSize" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="BitNum" :label-width="formLabelWidth">
          <el-select v-model="recordform.BitNumber">
            <el-option label="2" value="2"></el-option>
            <el-option label="4" value="4"></el-option>
            <el-option label="8" value="8"></el-option>
            <el-option label="16" value="16"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="SampleRate" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.SampleRate"
            autocomplete="off"
          ></el-input>
        </el-form-item>

        <el-form-item label="RecordBandWidth" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.RecordBandwidth"
            autocomplete="off"
          ></el-input>
        </el-form-item>

        <el-form-item label="RecordFrequency" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.RecordRXFrequency"
            autocomplete="off"
          ></el-input>
        </el-form-item>

        <el-form-item label="RecordXRgain" :label-width="formLabelWidth">
          <el-input
            v-model="recordform.RecordRXGain"
            autocomplete="off"
          ></el-input>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogrecord = false">取 消</el-button>
        <el-button type="primary" @click="addRecordData">确 定</el-button>
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
          ChanneIndex: 0,
          FileSize: "154090653200",
          BitNumber: "2",
          SampleRate: "122880000",
          RecordBandwidth: "10000000",
          RecordRXFrequency: "1575420000",
          RecordRXGain: "40",
          id: 5,
        },
        {
          ChanneIndex: "1",
          FileSize: "154090653200",
          BitNumber: "2",
          SampleRate: "122880000",
          RecordBandwidth: "10000000",
          RecordRXFrequency: "1575420000",
          RecordRXGain: "40",
          id: 6,
        },
        {
          ChanneIndex: "1",
          FileSize: "154090653200",
          BitNumber: "8",
          SampleRate: "122880000",
          RecordBandwidth: "10000000",
          RecordRXFrequency: "1575420000",
          RecordRXGain: "40",
          id: 7,
        },
        {
          ChanneIndex: "0",
          FileSize: "154090653200",
          BitNumber: "8",
          SampleRate: "122880000",
          RecordBandwidth: "10000000",
          RecordRXFrequency: "1575420000",
          RecordRXGain: "40",
          id: 8,
        },
      ],
      recordIndex: [],
      removeData: [],
      recordboolen: false,

      recordboolen0: true,
      recordboolen1: true,

      isclick: true, //用来判断提示不要频繁点击的布尔值

      dialogVisible: false,
      dialogrecord: false,

      recordform: {
        id: 1,
        ChanneIndex: 0,
        FileSize: "154090653200",
        BitNumber: "2",
        SampleRate: "122880000",
        RecordBandwidth: "10000000",
        RecordRXFrequency: "1575420000",
        RecordRXGain: "40",
      },

      formLabelWidth: "120px",
    };
  },
  created() {
    // this.getRecord();
  },
  methods: {
    //record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分
    getRecord() {
      //获取record数据
      // this.axios.get("/record").then((res) => {
      //   this.record = res.data;
      // });
    },
    addRecordData() {
      //添加新的数据到record
      this.dialogrecord = false;

      this.record.push(this.recordform);
      this.recordform = {
        id: this.recordform.id,
        ChanneIndex: this.recordform.ChanneIndex,
        FileSize: this.recordform.FileSize,
        BitNumber: this.recordform.BitNumber,
        SampleRate: this.recordform.SampleRate,
        RecordBandwidth: this.recordform.RecordBandwidth,
        RecordRXFrequency: this.recordform.RecordRXFrequency,
        RecordRXGain: this.recordform.RecordRXGain,
      };

      //模拟数据
      // this.axios.post("/record", this.recordform).then((res) => {
      //   // console.log(res);
      //   // console. log(1);
      //   this.getRecord();
      // });
    },
    Checkedrecord(item) {
      if (item.checked == true) {
        this.recordboolen = true;
        this.$nextTick(() => {
          if (this["recordboolen" + item.ChanneIndex]) {
            this["recordboolen" + item.ChanneIndex] = false;
            console.log(item.checked);
            item.checked = true;
            this.removeData.push(item.id);
          } else {
            item.checked = false;
            this["recordboolen" + item.ChanneIndex] = this.record.every(
              (val) => {
                if (val.ChanneIndex == item.ChanneIndex) {
                  item.checked = false;
                  return val.checked == false;
                } else {
                  return true;
                }
              }
            );
          }
        });
      } else {
        console.log(item.checked);
        this.recordboolen = false;
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
          })
          .catch((err) => {
            console.log(err);
          });
      } else {
        return;
      }

      //删除复选框选中的信息
      // if (this.recordboolen == true) {
      //   this.$confirm("确定要删除这条信息吗？")
      //     .then((_) => {
      //       for (let i = 0; i < this.recordid.length; i++) {
      //         this.axios.delete("/record/" + this.recordid[i]).then((res) => {
      //           this.getRecord();
      //           this.recordid = [];
      //           this.recordboolen0 = false;
      //           this.recordboolen1 = false;
      //           this.record.checked = false;
      //           this.recordboolen = false;
      //         });
      //       }
      //     })
      //     .catch((err) => {
      //       this.recordboolen0 = false;
      //       this.recordboolen1 = false;
      //       this.record.checked = false;
      //       console.log(err);
      //     });
      // } else {
      //   return;
      // }
    },
    StartRecord() {
      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "AddFileInfo",
              FileInformations: [
                {
                  FileName: "NA",
                  FileSize: 15667788899,
                  BitNumber: 16,
                  SampleRate: 122880000,
                  RecordBandwidth: 10000000,
                  RecordRXFrequency: 1575420000,
                  RecordRXGain: 50,
                  RecordChannelIndex: 0,
                },
                {
                  FileName: "NA",
                  FileSize: 15667788899,
                  BitNumber: 16,
                  SampleRate: 122880000,
                  RecordBandwidth: 10000000,
                  RecordRXFrequency: 1227600000,
                  RecordRXGain: 50,
                  RecordChannelIndex: 1,
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
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

        //关闭TCP连接
        ws.close();
        ws.onclose = function (e) {
          console.log(e);
        };
      };

      //socket请求----

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
    },
    close() {
      this.dialogrecord = false;
    },
    StartSynRecord() {
      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "AddFileInfo",
              FileInformations: [
                {
                  FileName: "NA",
                  FileSize: 15667788899,
                  BitNumber: 16,
                  SampleRate: 122880000,
                  RecordBandwidth: 10000000,
                  RecordRXFrequency: 1575420000,
                  RecordRXGain: 50,
                  RecordChannelIndex: 0,
                },
                {
                  FileName: "NA",
                  FileSize: 15667788899,
                  BitNumber: 16,
                  SampleRate: 122880000,
                  RecordBandwidth: 10000000,
                  RecordRXFrequency: 1227600000,
                  RecordRXGain: 50,
                  RecordChannelIndex: 1,
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
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

        //关闭TCP连接
        ws.close();
        ws.onclose = function (e) {
          console.log(e);
        };
      };

      //socket请求----

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
    },
  },
};
</script>

<style scoped>
#table {
  width: 100%;
  height: auto;
  overflow-x: scroll;
}
table {
  width: 90%;
  height: auto;
  /* background-color: pink; */
  margin: 0rem auto;
}
table th {
  height: 5rem;
  font-size: 1.3rem;
  border-bottom: 1px solid gainsboro;
}
table td {
  height: 5rem;
  /* background-color: aqua; */
  text-align: center;
  border-bottom: 1px solid gainsboro;
  color: rgb(151, 151, 146);
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
</style>