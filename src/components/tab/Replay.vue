
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
        <tr v-for="(item, index) in replay" ref="allchecked" :key="index" id="scrolltable">
          <td>
            <!-- 配置按钮在这 -->
            <el-button
              type="warning"
              icon="el-icon-document"
              circle
              @click="showplay(index)"
            ></el-button>
            <!-- 配置按钮在这 -->
          </td>
          <td>{{ item.FileName }}</td>

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
              @change="Checkedreplay(item, index)"
            ></el-checkbox>
          </td>

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
      <el-button class="btn query" @click="StopRecord">Stop Record</el-button>
    </p>
    <p>
      <el-button class="btn query" @click="StartSynPlay"
        >Start SynReplay</el-button
      >
      <el-button class="btn query" @click="StopSynPlay"
        >Stop SynReplay</el-button
      >
      <el-button class="btn query" @click="StopSynRecord"
        >Stop SynRecord</el-button
      >
    </p>

    <!-- 嵌套的表单 点击配置显示的对话框在这-->
    <el-dialog
      title="新建录制配置"
      :visible="dialogreplay"
      id="dialogreplay"
      :before-close="close"
    >
      <el-form>
        <el-form-item label="PlayChannelIndex" :label-width="formLabelWidth">
          <el-select v-model="PlayChannelIndex" placeholder="0">
            <el-option label="0" value="0"></el-option>
            <el-option label="1" value="1"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="PlayTXGain" :label-width="formLabelWidth">
          <el-input
            autocomplete="off"
            v-model="PlayTXGain"
            placeholder="50"
          ></el-input>
        </el-form-item>

        <el-form-item label="PlayTXFrequency" :label-width="formLabelWidth">
          <el-input
            autocomplete="off"
            v-model="PlayTXFrequency"
            placeholder="1575420000"
          ></el-input>
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogreplay = false">CANCEL</el-button>
        <el-button type="primary" @click="Accept()">ACCEPT</el-button>
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
      cloneReplay: [],
      dialogreplay: false, //对话框是否显示

      ischecked:true,

      replayboolen: false, //用来判断删除是否可点击
      formLabelWidth: "120px", //对话框的长度
      isclick: true, //用来判断提示不要频繁点击的布尔值
      boolen0: true,
      boolen1: true,
      index: "",
      //配置回放的对话框绑定的数据
      replayIDForm: {},

      removeData: [], //需要删除的数据
      stopRcordData: [], //需要停止录制的数据
      playData: [], //需要回放的相关数据
      stopPlayData: [], //需要停止回放的相关数据
      PlayChannelIndex: "",
      PlayTXGain: "",
      PlayTXFrequency: "",
      curren_index: "",
    };
  },
  methods: {
    //replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分
    getReplay() {
      //获取数据

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
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
        // console.log(JSON.parse(e.data).cmd.FileInformations);
        that.replay = JSON.parse(e.data).cmd.FileInformations;

        //关闭socket连接
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
    Checkedreplay(item, index) {

      if (item.checked) {
        // console.log(item.checked);
        this.$nextTick(() => {
          if (this["boolen" + item.RecordChannelIndex]) {
            this["boolen" + item.RecordChannelIndex] = false;
            item.checked = true;

            item.checked=this.ischecked
            console.log(item.checked);
            console.log(this.ischecked);

            //要删除的数据操作
            this.removeData.push(item.FileName);

            //要停止录制的相关数据操作
            this.stopRcordData.push({
              FileName: item.FileName,
              PlayChannelIndex: item.RecordChannelIndex,
            });

            //要停止回放的数据的数据操作
            this.stopPlayData.push({
              FileName: this.replay[index].FileName,
              PlayChannelIndex: parseInt(this.replay[index].RecordChannelIndex),
            });
            // console.log(this.stopRcordData);
            this.replayboolen = true;
          } else {
            item.checked = false;
            this.replayboolen = false;
            // console.log(item.checked);
            this["boolen" + item.RecordChannelIndex] = this.replay.every(
              (val) => {
                if (val.RecordChannelIndex == item.RecordChannelIndex) {
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
        this.replayboolen = false;
      }
    },
    Deletreplay() {
      //删除复选框选中的信息
      var ids = this.removeData;
      var item;
      for (var i = 0; i < ids.length; i++) {
        item = ids[i];
        console.log(item);
      }

      if (this.replayboolen == true) {
        this.$confirm("确定要删除这条信息吗？")
          .then((_) => {
            //socket请求----
            var ws = new WebSocket("ws://192.168.1.203:9001");
            ws.onopen = function (e) {
              // console.log(ws.readyState);
              for (var i = 0; i < ids.length; i++) {
                item = ids[i];
                console.log(item);
                ws.send(
                  JSON.stringify({
                    cmd: {
                      APIName: "DeleteFileInfo",
                      FileInformations: {
                        FileName: item,
                      },
                    },
                  })
                );
              }
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
            this.reload();
            this.getReplay();
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
      setTimeout(() => {
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
      }, 1000);

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
    showplay(index) {
      if (this.replayboolen == true) {
        this.curren_index = index;
        // console.log(index);
        this.dialogreplay = true;
      } else {
        return;
      }
    },
    Accept() {
      //配置（对话框）
      // console.log(this.PlayChannelIndex);
      // console.log(this.PlayTXGain);
      // console.log(this.PlayTXFrequency);
      // console.log(this.replay[this.curren_index]);

      this.playData.push({
        FileName: this.replay[this.curren_index].FileName,
        PlayTXFrequency: parseInt(this.PlayTXFrequency),
        PlayTXGain: parseInt(this.PlayTXGain),
        PlayChannelIndex: parseInt(this.PlayChannelIndex),
      });
      this.dialogreplay = false;
      return false;
    },
    close() {
      this.dialogreplay = false;
    },
    StartPlay() {
      //下发StartPlay API请求  多条下发
      var data = this.playData;
      var item;
      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        for (var i = 0; i < data.length; i++) {
          item = data[i];
          ws.send(
            JSON.stringify({
              cmd: {
                APIName: "StartPlay",
                FileInformations: [item],
              },
            })
          );
        }
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

    StartSynPlay() {
      //下发StartPlay API请求  组合多条数据在一个数组里面进行下发请求
      var data;

      setTimeout(() => {
        data = this.playData;
        console.log(data);

        console.log(
          JSON.stringify({
            cmd: {
              APIName: "StartPlay",
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
                APIName: "StartPlay",
                FileInformations: data,
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

          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };
      }, 800);
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

      //this.stopPlayData
      console.log(this.stopPlayData);
      var data = this.stopPlayData;
      var item;

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        for (var i = 0; i < data.length; i++) {
          item = data[i];
          ws.send(
            JSON.stringify({
              cmd: {
                APIName: "StopPlay",
                FileInformations: [item],
              },
            })
          );
        }
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

        //关闭TCP连接
        ws.close();
        ws.onclose = function (e) {
          console.log(e);
        };
      };

      //socket请求----

      this.getReplay();

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

      //this.stopPlayData
      var data;

      setTimeout(() => {
        data = this.stopPlayData;
        console.log(data);

        console.log(
          JSON.stringify({
            cmd: {
              APIName: "StopPlay",
              FileInformations: data,
            },
          })
        );

        //socket请求----
        var ws = new WebSocket("ws://192.168.1.203:9001");
        ws.onopen = function (e) {
          // console.log(ws.readyState);
          ws.send(
            JSON.stringify({
              cmd: {
                APIName: "StopPlay",
                FileInformations: data,
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

          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };
      }, 800);

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
    StopRecord() {
      //下发停止录制StopRecord请求  单条数据下发
      var data = this.stopRcordData;
      var item;
      console.log(data);

      //socket请求----
      var ws = new WebSocket("ws://192.168.1.203:9001");
      ws.onopen = function (e) {
        // console.log(ws.readyState);
        for (var i = 0; i < data.length; i++) {
          item = data[i];
          ws.send(
            JSON.stringify({
              cmd: {
                APIName: "StopRecord",
                FileInformations: [item],
              },
            })
          );
        }
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
            that.$message.error("失败!");
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

    StopSynRecord() {
      //下发停止录制StopRecord请求  组合数据下发
      console.log(this.stopRcordData);

      var data;

      setTimeout(() => {
        data = this.stopRcordData;
        console.log(data);

        console.log(
          JSON.stringify({
            cmd: {
              APIName: "StopRecord",
              FileInformations: data,
            },
          })
        );

        //socket请求----
        var ws = new WebSocket("ws://192.168.1.203:9001");
        ws.onopen = function (e) {
          // console.log(ws.readyState);
          ws.send(
            JSON.stringify({
              cmd: {
                APIName: "StopRecord",
                FileInformations: data,
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
              that.$message.error("失败!");
              break;
          }
          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };
      }, 800);
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