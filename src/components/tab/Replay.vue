
<template>
  <div class="replay">
    <div id="table">
      <table id="replaytable">
        <tr>
          <th>
            Replay<br />
            Config
          </th>
          <th>FileName</th>

          <!-- 删除按钮在这 -->
          <th>
            <el-button
              type="warning"
              icon="el-icon-delete"
              circle
              @click="Deletreplay()"
            ></el-button>
          </th>
          <!-- 删除按钮在这 -->

          <th>RecordChannelIndex</th>
          <th>FileSize(Byte)</th>
          <th>BitNum</th>
          <th>SampleRate(Hz)</th>
          <th>RecordBandWidth(Hz)</th>
          <th>RecordFrequency(Hz)</th>
          <th>RecordXRgain(Hz)</th>
        </tr>
        <tr v-for="item in replay" :key="item.id" id="scrolltable">
          <td>
            <el-button
              type="warning"
              icon="el-icon-document"
              circle
              @click="dialogreplay = true"
            ></el-button>
          </td>
          <td>{{ item.FileName }}</td>

          <!-- 复选框在这 -->
          <td>
            <span
              class="status"
              :class="[
                item.isRecording == 1 && item.isPlaying == 0 ? 'red' : '',
                item.isRecording == 0 && item.isPlaying == 1 ? 'green' : '',
                item.isRecording == 0 && item.isPlaying == 0 ? 'gray' : '',
              ]"
            ></span
            >&nbsp;
            <el-checkbox
              class="select"
              v-model="item.checked"
              @change="Checkedreplay(item.FileName, item.checked)"
            ></el-checkbox>
          </td>
          <!-- 复选框在这 -->

          <td>{{ item.RecordChannelIndex }}</td>
          <td>{{ item.FileSize }}</td>
          <td>{{ item.BitNumber }}</td>
          <td>{{ item.SampleRate }}</td>
          <td>{{ item.RecordBandwidth }}</td>
          <td>{{ item.RecordRXFrequency }}</td>
          <td>{{ item.RecordRXGain }}</td>
        </tr>
      </table>
    </div>

    <p>RemainHarddisk Size: &nbsp;&nbsp;&nbsp;11670744000 Byte</p>

    <p>
      <el-button class="btn query" @click="getReplay"
        >Query File Info</el-button
      >
      <el-button class="btn query">Query Replaying BoardIndex</el-button>
    </p>
    <p>
      <el-button class="btn query" @click="StartPlay">Start Replay</el-button>
      <el-button class="btn query" @click="StopPlay">Stop Replay</el-button>
      <el-button class="btn query">Stop Record</el-button>
    </p>
    <p>
      <el-button class="btn query" @click="StartSynPlay"
        >Start SynReplay</el-button
      >
      <el-button class="btn query" @click="StopSynPlay">Stop SynReplay</el-button>
      <el-button class="btn query">Stop SynRecord</el-button>
    </p>

    <!-- 嵌套的表单 -->
    <el-dialog title="新建录制配置" :visible="dialogreplay" id="dialogreplay">
      <el-form>
        <el-form-item label="PlayBoardIndex" :label-width="formLabelWidth">
          <el-select>
            <el-option label="0" value="0"></el-option>
            <el-option label="1" value="1"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="PlatTXGain" :label-width="formLabelWidth">
          <el-input autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="PlayTXFrequency" :label-width="formLabelWidth">
          <el-input autocomplete="off"></el-input>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogreplay = false">CANCEL</el-button>
        <el-button type="primary" @click="Accept">ACCEPT</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      replay: [
        // {
        //   BitNumber: 16,
        //   FileName: "gnss_16457582170#0_20220225_110355_293_110914_055.bin",
        //   FileSize: "15667788899",
        //   RecordBandwidth: "10000000",
        //   RecordChannelIndex: 0,
        //   RecordRXFrequency: "1575420000",
        //   RecordRXGain: 50,
        //   SampleRate: "122880000",
        //   isPlaying: 0,
        //   isRecording: 1,
        // },
        // {
        //   BitNumber: 16,
        //   FileName: "gnss_16457582170#0_20220225_110355_293_110914_055.bin",
        //   FileSize: "15667788899",
        //   RecordBandwidth: "10000000",
        //   RecordChannelIndex: 0,
        //   RecordRXFrequency: "1575420000",
        //   RecordRXGain: 50,
        //   SampleRate: "122880000",
        //   isPlaying: 1,
        //   isRecording: 0,
        // },
        // {
        //   BitNumber: 16,
        //   FileName: "gnss_16457582170#0_20220225_110355_293_110914_055.bin",
        //   FileSize: "15667788899",
        //   RecordBandwidth: "10000000",
        //   RecordChannelIndex: 0,
        //   RecordRXFrequency: "1575420000",
        //   RecordRXGain: 50,
        //   SampleRate: "122880000",
        //   isPlaying: 1,
        //   isRecording: 1,
        // },
        // {
        //   BitNumber: 16,
        //   FileName: "gnss_16457582170#0_20220225_110355_293_110914_055.bin",
        //   FileSize: "15667788899",
        //   RecordBandwidth: "10000000",
        //   RecordChannelIndex: 0,
        //   RecordRXFrequency: "1575420000",
        //   RecordRXGain: 50,
        //   SampleRate: "122880000",
        //   isPlaying: 0,
        //   isRecording: 0,
        // },
      ],
      dialogreplay: false,
      removeData: "",
      replayboolen: false,
      formLabelWidth: "120px",
      isclick: true,
    };
  },
  methods: {
    //replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分
    getReplay() {
      //获取数据
      //模拟replay数据
      // this.axios.get("/QueryFileInfo").then((res) => {
      //   this.replay = res.data;
      //   // console.log(this.replay);
      // });

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "QueryFileInfo",
              RemainHarddiskSize: -1,
              FileNumber: -1,
              FileInformations: [
                {
                  FileName: "NA",
                  FileSize: 0,
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
        console.log(JSON.parse(e.data).cmd.FileInformations);
        // that.replay=JSON.parse(e.data).cmd.FileInformations
        that.replay = JSON.parse(e.data).cmd.FileInformations;
      };
      ws.onclose = function (e) {
        console.log(e);
        ws.close(); //关闭TCP连接
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
    Checkedreplay(FileName, istrue) {
      //复选框选中的事件
      console.log(FileName);
      if (istrue == true) {
        this.removeData = FileName;
        console.log(this.removeData);
        this.replayboolen = istrue;
      } else {
        this.replayboolen = istrue;
      }
    },
    Deletreplay() {
      //删除复选框选中的信息
      var ids = this.removeData;
      if (this.replayboolen == true) {
        this.$confirm("确定要删除这条信息吗？")
          .then((_) => {
            //socket请求----
            var ws = new WebSocket("ws://192.168.1.203:9001");
            ws.onopen = function (e) {
              // console.log(ws.readyState);
              ws.send(
                JSON.stringify({
                  cmd: {
                    APIName: "DeleteFileInfo",
                    FileInformations: {
                      FileName: ids,
                    },
                  },
                })
              );
            };
            var that = this;
            ws.onmessage = function (e) {
              console.log(JSON.parse(e.data).cmd);
              var statu = JSON.parse(e.data).cmd.ResultCode;
              switch (statu) {
                case 0:
                  that.$message({
                    message: "成功",
                    type: "success",
                  });
                  break;
                case 1:
                  that.$message.error("文件不存在!");
                  break;
                case 2:
                  that.$message({
                    message: "文件正在录制!",
                    type: "warning",
                  });
                  break;
                case 3:
                  that.$message({
                    message: "文件正在回放!",
                    type: "warning",
                  });
                  break;
              }
            };
            ws.onclose = function (e) {
              console.log(e);
              ws.close(); //关闭TCP连接
            };
            //socket请求----
          })
          .catch((err) => {
            this.replay.checked = false;
            console.log(err);
          });
      } else {
        return;
      }

      this.getReplay();
      this.replay.checked = false;

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

      // if (this.replayboolen == true) {
      //   this.$confirm("确定要删除这条信息吗？")
      //     .then((_) => {
      //       for (var i = 0; i < this.replayid.length; i++) {
      //         this.axios
      //           .delete("/QueryFileInfo/" + this.replayid[i])
      //           .then((res) => {
      //             this.getReplay();
      //             this.replayboolen = false;
      //           });
      //       }
      //     })
      //     .catch((err) => {
      //       this.replay.checked = false;
      //       console.log(err);
      //     });
      // } else {
      //   return;
      // }
    },

    Accept() {
      //配置（对话框）
      this.dialogreplay = false;
    },
    StartPlay() {
      //下发StartPlay API请求  多条下发

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "StartPlay",
              FileInformations: [
                {
                  FileName:
                    "gnss_3345678683688856#0_20211227_103250_325_115910_456.bin",
                  PlayTXFrequency: 1575420000,
                  PlayTXGain: 40,
                  PlayChannelIndex: 0,
                },
                {
                  FileName:
                    "gnss_3345678683688856#1_20211227_103250_325_115910_456.bin",
                  PlayTXFrequency: 1575420000,
                  PlayTXGain: 40,
                  PlayChannelIndex: 1,
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
        var statu = JSON.parse(e.data).cmd.ResultCode;
        switch (statu) {
          case 0:
            that.$message({
              message: "成功",
              type: "success",
            });
            break;
          case 1:
            that.$message.error("文件不存在!");
            break;
          case 2:
            that.$message({
              message: "板卡ID已被使用!",
              type: "warning",
            });
            break;
          case 3:
            that.$message({
              message: "其他失败!",
              type: "warning",
            });
            break;
        }
      };
      ws.onclose = function (e) {
        console.log(e);
        ws.close(); //关闭TCP连接
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
    StartSynPlay() {
      //下发StartPlay API请求  单条数据下发

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "StartPlay",
              FileInformations: [
                {
                  FileName:
                    "gnss_33456786836456230_20211227_103250_325_115910_456.bin",
                  PlayTXFrequency: 1575420000,
                  PlayTXGain: 40,
                  PlayChannelIndex: 0,
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
        var statu = JSON.parse(e.data).cmd.ResultCode;
        switch (statu) {
          case 0:
            that.$message({
              message: "成功",
              type: "success",
            });
            break;
          case 1:
            that.$message.error("文件不存在!");
            break;
          case 2:
            that.$message({
              message: "板卡ID已被使用!",
              type: "warning",
            });
            break;
          case 3:
            that.$message({
              message: "其他失败!",
              type: "warning",
            });
            break;
        }
      };
      ws.onclose = function (e) {
        console.log(e);
        ws.close(); //关闭TCP连接
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
    StopPlay() {
      //下发StartPlay API请求  单条数据下发

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "StopPlay",
              FileInformations: [
                {
                  FileName:
                    "gnss_3345678683688856#0_20211227_103250_325_115910_456.bin",
                  PlayChannelIndex: 0,
                },
                {
                  FileName:
                    "gnss_3345678683688856#1_20211227_103250_325_115910_456.bin",
                  PlayChannelIndex: 1,
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
        var statu = JSON.parse(e.data).cmd.ResultCode;
        switch (statu) {
          case 0:
            that.$message({
              message: "成功",
              type: "success",
            });
            break;
          case 1:
            that.$message.error("文件不存在!");
            break;
        }
      };
      ws.onclose = function (e) {
        console.log(e);
        ws.close(); //关闭TCP连接
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
    StopSynPlay() {
      //下发StartPlay API请求  单条数据下发

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        ws.send(
          JSON.stringify({
            cmd: {
              APIName: "StopPlay",
              FileInformations: [
                {
                  FileName:
                    "gnss_3345678683324512_20211227_103250_325_115910_456.bin",
                  PlayChannelIndex: 0,
                },
              ],
            },
          })
        );
      };
      var that = this;
      ws.onmessage = function (e) {
        var statu = JSON.parse(e.data).cmd.ResultCode;
        switch (statu) {
          case 0:
            that.$message({
              message: "成功",
              type: "success",
            });
            break;
          case 1:
            that.$message.error("文件不存在!");
            break;
        }
      };
      ws.onclose = function (e) {
        console.log(e);
        ws.close(); //关闭TCP连接
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
  width: 100%;
  height: auto;
  /* background-color: pink; */
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

.replay >>> table .el-checkbox__input.is-checked .el-checkbox__inner {
  width: 16px;
  height: 16px;
  background-color: rgb(245, 154, 35);
  background-size: cover;
  border: rgb(245, 154, 35) 2px solid;
}
.replay >>> table .el-checkbox__inner {
  width: 16px;
  height: 16px;
  border: rgb(245, 154, 35) 2px solid;
}
.replay >>> p .el-button {
  background-color: rgb(245, 154, 35);
  color: white;
  font-size: 1rem;
  margin-left: 50px;
  width: 250px;
}
.replay >>> table .status {
  width: 22px;
  height: 22px;
  /* background-color: rgb(217, 0, 27); */
  /* border: 1px solid black; */
  display: inline-block;
  border-radius: 50%;
  position: relative;
  top: 5px;
}
.red {
  background-color: rgb(217, 0, 27) !important;
}
.gray {
  background-color: rgb(170, 170, 170) !important;
}
.green {
  background-color: rgb(149, 242, 4) !important;
}
.white {
  background-color: white !important;
}
</style>