
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
          <th>FileCurrentSize(Byte)</th>
          <th>BitNum</th>
          <th>SampleRate(Hz)</th>
          <th>RecordBandWidth(Hz)</th>
          <th>RecordFrequency(Hz)</th>
          <th>RecordXRgain(Hz)</th>
          <th>Describe</th>
        </tr>
        <tr
          v-for="(item, index) in replay"
          ref="allchecked"
          :key="index"
          id="scrolltable"
        >
          <td class="td">
            <!-- 配置按钮在这 -->
            <el-button
              type="warning"
              icon="el-icon-document"
              circle
              @click="showplay(index)"
            ></el-button>
            <!-- 配置按钮在这 -->
          </td>
          <td id="fileName">{{ item.FileName }}</td>

          <td class="td">
            <span
              class="status"
              :class="[
                item.isRecording == 1 && item.isPlaying == 0 ? 'red' : '',
                item.isRecording == 0 && item.isPlaying == 1 ? 'green' : '',
                item.isRecording == 0 && item.isPlaying == 0 ? 'gray' : '',
              ]"
            ></span
            >&nbsp;
            <input
              type="checkbox"
              class="select"
              @change="Checkedreplay($event, item, index)"
              ref="checkbox"
            />
          </td>

          <td class="td">{{ item.RecordChannelIndex }}</td>
          <td class="td">{{ item.FileCurrentSize }}</td>
          <td class="td">{{ item.BitNumber }}</td>
          <td class="td">{{ item.SampleRate }}</td>
          <td class="td">{{ item.RecordBandwidth }}</td>
          <td class="td">{{ item.RecordRXFrequency }}</td>
          <td class="td">{{ item.RecordRXGain }}</td>
          <td class="td">
            <el-button type="warning" class="btn query" @click="ShowMore(item)"
              >View more information</el-button
            >
          </td>
          <!-- 嵌套的表单 点击配置显示的对话框在这-->
          <el-dialog
            title="新建录制配置"
            :visible="dialogreplay"
            id="dialogreplay"
            :before-close="close"
            :modal="modal"
            class="ui-widget-content"
          >
            <el-form>
              <el-form-item
                label="PlayChannelIndex"
                :label-width="formLabelWidth"
              >
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

              <el-form-item
                label="PlayTXFrequency(Hz)"
                :label-width="formLabelWidth"
              >
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
          <!-- 嵌套的表单 点击配置显示的对话框在这-->
        </tr>
      </table>
    </div>

    <p>RemainHarddisk Size: &nbsp;&nbsp;&nbsp;{{ RemainHarddiskSize }} Byte</p>
    <p>
      RemainHarddisk Size: &nbsp;&nbsp;&nbsp;{{ RemainExHarddiskSize }} Byte
    </p>

    <p>
      <el-button class="btn query" @click="getReplay"
        >Query File Info</el-button
      >
      <el-button class="btn query" @click="QueryPlayId"
        >Query Replaying ChannelIndex</el-button
      >
    </p>
    <p>
      <el-button class="btn query" @click="StartPlay" v-preventReClick="5000"
        >Start Replay</el-button
      >
      <el-button class="btn query" @click="StopPlay" v-preventReClick="5000"
        >Stop Replay</el-button
      >
      <el-button class="btn query" @click="StopRecord" v-preventReClick="5000"
        >Stop Record</el-button
      >
    </p>
    <p>
      <el-button class="btn query" @click="StartSynPlay" v-preventReClick="5000"
        >Start SynReplay</el-button
      >
      <el-button class="btn query" @click="StopSynPlay" v-preventReClick="5000"
        >Stop SynReplay</el-button
      >
      <el-button
        class="btn query"
        @click="StopSynRecord"
        v-preventReClick="5000"
        >Stop SynRecord</el-button
      >
    </p>
  </div>
</template>

<script>
import $ from "jquery";
import "jquery-ui-dist/jquery-ui";
import "jquery-ui-dist/jquery-ui.min.css";

export default {
  data() {
    return {
      replay: [
        // {
        //   FileName: "gnss_3345678683688856_20211227_103250_325_115910_456.bin",
        //   FileSize: 154090653200,
        //   BitNumber: 16,
        //   SampleRate: 122880000,
        //   RecordBandwidth: 10000000,
        //   RecordRXFrequency: 1575420000,
        //   RecordRXGain: 40,
        //   RecordChannelIndex: 0,
        //   Describe: "GPS,Shenzhen Skyworth Industrial Park",
        //   FileCurrentSize: 154090653200,
        //   isRecording: 0,
        //   isPlaying: 1,
        //   isUseExDisk: 1,
        // },
        // {
        //   FileName: "gnss_3345678683656779_20211527_160150_115.bin",
        //   FileSize: 8541255667,
        //   BitNumber: 16,
        //   SampleRate: 122880000,
        //   RecordBandwidth: 10000000,
        //   RecordRXFrequency: 1575420000,
        //   RecordRXGain: 40,
        //   RecordChannelIndex: 0,
        //   Describe: "GPS,Shenzhen Skyworth Industrial Park",
        //   FileCurrentSize: 32461111,
        //   isRecording: 1,
        //   isPlaying: 0,
        //   isUseExDisk: 1,
        // },
        // {
        //   FileName:
        //     "gnss_3345678683688856#0_20211227_103250_325_115910_456.bin",
        //   FileSize: 154090653200,
        //   BitNumber: 16,
        //   SampleRate: 122880000,
        //   RecordBandwidth: 10000000,
        //   RecordRXFrequency: 1575420000,
        //   RecordRXGain: 40,
        //   RecordChannelIndex: 0,
        //   Describe: "GPS,Shenzhen Skyworth Industrial Park",
        //   FileCurrentSize: 154090653200,
        //   isRecording: 0,
        //   isPlaying: 0,
        //   isUseExDisk: 0,
        // },
        // {
        //   FileName:
        //     "gnss_3345678683688856#1_20211227_103250_325_115910_456.bin",
        //   FileSize: 154090653200,
        //   BitNumber: 16,
        //   SampleRate: 122880000,
        //   RecordBandwidth: 10000000,
        //   RecordRXFrequency: 1575420000,
        //   RecordRXGain: 40,
        //   RecordChannelIndex: 1,
        //   Describe: "GPS,Shenzhen Skyworth Industrial Park",
        //   FileCurrentSize: 154090653200,
        //   isRecording: 0,
        //   isPlaying: 0,
        //   isUseExDisk: 0,
        // },
        // {
        //   FileName: "gnss_3345678683656779#0_20211527_160150_115.bin",
        //   FileSize: 8541255666,
        //   BitNumber: 16,
        //   SampleRate: 122880000,
        //   RecordBandwidth: 10000000,
        //   RecordRXFrequency: 1575420000,
        //   RecordRXGain: 40,
        //   RecordChannelIndex: 0,
        //   Describe: "GPS,Shenzhen Skyworth Industrial Park",
        //   FileCurrentSize: 568733,
        //   isRecording: 1,
        //   isPlaying: 0,
        //   isUseExDisk: 1,
        // },
        // {
        //   FileName: "gnss_3345678683656779#1_20211527_160150_115.bin",
        //   FileSize: 8541255666,
        //   BitNumber: 16,
        //   SampleRate: 122880000,
        //   RecordBandwidth: 10000000,
        //   RecordRXFrequency: 1575420000,
        //   RecordRXGain: 40,
        //   RecordChannelIndex: 1,
        //   Describe: "GPS,Shenzhen Skyworth Industrial Park",
        //   FileCurrentSize: 568733,
        //   isRecording: 1,
        //   isPlaying: 0,
        //   isUseExDisk: 1,
        // },
      ],
      modal: false, //不要蒙层
      RemainHarddiskSize: 11670744000, //用来存储磁盘大小
      RemainExHarddiskSize: 11670744000, //用来存储外置磁盘大小
      IndexList: [], //用来控制只可以选中两个index不一样的复选框
      ChannelIndex: [], //板卡
      dialogreplay: false, //对话框是否显示

      replayboolen: false, //用来判断删除是否可点击
      formLabelWidth: "155px", //对话框的长度
      isclick: true, //用来判断提示不要频繁点击的布尔值
      index: "",

      removeData: [], //需要删除的数据
      stopRcordData: [], //需要停止录制的数据
      playData: [], //需要回放的相关数据
      stopPlayData: [], //需要停止回放的相关数据
      queryIndex: [], //查询使用的办卡ID的数据
      FileName: [], //组合下发判断是否选择的是一样的数据

      //配置回放对话框绑定的数据
      PlayChannelIndex: 0,
      PlayTXGain: "",
      PlayTXFrequency: "",

      curren_index: "", //获取index，取到当下点击的是哪一条数据
    };
  },
  methods: {
    created() {
      //拖拽事件
      $(function () {
        $("#dialogreplay").draggable();
      });
    },
    ShowMore(item) {
      console.log(item.isUseExDisk);
      var save;
      var Describe = item.Describe;
      if (item.isUseExDisk == 0) {
        save = "Built in storage";
      } else {
        save = "External storage";
      }

      this.$alert(
        "<p><strong>Storage location&nbsp;:&nbsp;</strong>" +
          save +
          "</p><br/><p><strong>Describe&nbsp;:&nbsp;</strong>" +
          Describe +
          "</p>",
        "View more information",
        {
          confirmButtonText: "Close",
          dangerouslyUseHTMLString: true,
          callback: (action) => {
            // this.$message({
            //   type: "info",
            //   message: `action: ${action}`,
            // });
          },
        }
      );
    },
    getReplay() {
      this.ChannelIndex = this.$store.state.ChannelIndex;
      console.log(this.ChannelIndex);
      //获取数据
      // console.log(111);
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
        if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
          that.$message.error("General error!"); //通用错误
        } else {
          that.replay = JSON.parse(e.data).cmd.FileInformations;

          // that.replay = that.replay.sort((a, b) =>
          //   a.FileName > b.FileName ? 1 : b.FileName > a.FileName ? -1 : 0
          // );

          that.RemainHarddiskSize = JSON.parse(e.data).cmd.RemainHarddiskSize;
          that.RemainExHarddiskSize = JSON.parse(
            e.data
          ).cmd.RemainExHarddiskSize;

          var arr = JSON.parse(e.data).cmd.FileInformations;
          var Allspace = JSON.parse(e.data).cmd.RemainHarddiskSize;
          var RecordData;
          var Availablespace;
          RecordData = arr.filter(function (item) {
            return item.isRecording == 1 && item.isPlaying == 0;
          });
          if (RecordData.length > 0) {
            for (var i = 0; i < RecordData.length; i++) {
              console.log(RecordData[i].FileSize);
              console.log(RecordData[i].FileCurrentSize);
              Availablespace =
                Allspace -
                (RecordData[i].FileSize - RecordData[i].FileCurrentSize);
              that.$store.commit("getAvailablespace", Availablespace);
            }
          }
          // console.log(Allspace);
          // console.log(RecordData);
          // console.log(Availablespace);
        }

        //关闭socket连接
        ws.close();
      };

      //socket请求----

      //取消所有的复选框的勾选
      this.$refs.checkbox.map(function (item) {
        item.checked = false;
      });
      this.IndexList = [];
      //取消所有的复选框的勾选

      //所有的需要操作的数据清空数组
      this.removeData = [];
      this.stopRcordData = [];
      this.playData = [];
      this.stopPlayData = [];
      this.FileName = [];
    },
    QueryPlayId() {
      //下发StartPlay API请求  多条下发

      if (this.replayboolen == true) {
        var data = this.queryIndex;
        console.log(
          JSON.stringify({
            cmd: {
              APIName: "QueryPlayId",
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
                APIName: "QueryPlayId",
                FileInformations: data,
              },
            })
          );
        };
        var that = this;
        ws.onmessage = function (e) {
          if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
            that.$message.error("General error!"); //通用错误
          } else {
            var statu = JSON.parse(e.data).cmd.FileInformations
              .PlayChannelIndexs;
            console.log(
              JSON.parse(e.data).cmd.FileInformations.PlayChannelIndexs
            );
            console.log(statu);
            switch (statu) {
              case 0:
                that.$message({
                  message: statu,
                  type: "success",
                });
                break;
              case 1:
                that.$message({
                  message: statu,
                  type: "success",
                });
                break;
              case (statu.length = 0):
                that.$message({
                  message: "No results found!", //未查询到结果
                  type: "warning",
                });
                break;
            }
          }

          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };

        //socket请求----

        //取消所有的复选框的勾选
        this.$refs.checkbox.map(function (item) {
          item.checked = false;
        });
        this.IndexList = [];
        //取消所有的复选框的勾选

        // //提示不要频繁点击
        // if (this.isclick) {
        //   this.isclick = false;
        //   setTimeout(() => {
        //     this.isclick = true;
        //   }, 4000);
        // } else {
        //   this.$message({
        //     message: "请不要频繁点击！",
        //     type: "warning",
        //   });
        // }
        // //提示不要频繁点击

        this.replayboolen = false;
      } else {
        return;
      }
    },
    Checkedreplay(e, item, index) {
      let ChannelIndex = item.RecordChannelIndex;
      if (e.target.checked == true) {
        if (this.IndexList.indexOf(ChannelIndex) == -1) {
          this.IndexList.unshift(ChannelIndex);
          this.replayboolen = true;

          //要开始回放的数据操作
          this.playData.push({
            FileName: item.FileName,
            PlayTXFrequency: parseInt(item.RecordRXFrequency),
            PlayTXGain: parseInt(item.RecordRXGain),
            PlayChannelIndex: parseInt(item.RecordChannelIndex),
          });

          console.log(this.playData);

          //要删除的数据操作
          this.removeData.push(item.FileName);
          // console.log(this.removeData);

          //要停止回放的数据的数据操作
          this.stopPlayData.push({
            FileName: this.replay[index].FileName,
            PlayChannelIndex: parseInt(this.replay[index].RecordChannelIndex),
          });
          // console.log(this.stopPlayData);

          //要停止录制的相关数据操作
          this.stopRcordData.push({
            FileName: item.FileName,
            PlayChannelIndex: item.RecordChannelIndex,
          });
          // console.log(this.stopPlayData);

          //查询使用的办卡ID的数据相关操作
          this.queryIndex = {
            FileName: this.replay[index].FileName,
            PlayChannelIndexs: parseInt(this.replay[index].RecordChannelIndex),
          };
          // console.log(this.queryIndex);

          //组合下发判断是否选择的是一样的数据
          this.FileName.push(item.FileName);
          // console.log(this.FileName);
        } else {
          e.target.checked = false;
          this.replayboolen = false;
        }
      } else {
        this.IndexList.splice(this.IndexList.indexOf(ChannelIndex), 1);
        this.removeData.splice(this.IndexList.indexOf(ChannelIndex), 1);
        this.stopPlayData.splice(this.IndexList.indexOf(ChannelIndex), 1);
        this.stopRcordData.splice(this.IndexList.indexOf(ChannelIndex), 1);
        this.FileName.splice(this.IndexList.indexOf(ChannelIndex), 1);
        this.replayboolen = false;
      }
      // console.log(e.target.checked);
    },
    Deletreplay() {
      //删除复选框选中的信息
      var data = this.removeData;
      var item;

      if (this.replayboolen == true) {
        this.$confirm("Are you sure you want to delete？") //确定要删除吗
          .then((_) => {
            //socket请求----
            var ws = new WebSocket("ws://192.168.1.203:9001");
            ws.onopen = function (e) {
              for (var i = 0; i < data.length; i++) {
                item = data[i];
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
              if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                that.$message.error("General error!"); //通用错误
              } else {
                var statu = JSON.parse(e.data).cmd.ResultCode;
                switch (statu) {
                  case 0:
                    that.$message({
                      message: "success", //成功
                      type: "success",
                    });
                    break;
                  case 1:
                    that.$message.error("file does not exist!"); //文件不存在
                    break;
                  case 2:
                    that.$message({
                      message: "The file is being recorded!", //文件正在录制
                      type: "warning",
                    });
                    break;
                  case 3:
                    that.$message({
                      message: "File playback in progress!", //文件正在回放
                      type: "warning",
                    });
                    break;
                }
              }

              //关闭TCP连接
              ws.close();
              ws.onclose = function (e) {
                console.log(e);
              };
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

      //取消所有的复选框的勾选
      this.$refs.checkbox.map(function (item) {
        item.checked = false;
      });
      this.IndexList = [];
      //取消所有的复选框的勾选

      //提示不要频繁点击
      setTimeout(() => {
        if (this.isclick) {
          this.isclick = false;
          setTimeout(() => {
            this.isclick = true;
          }, 4000);
        } else {
          this.$message({
            message: "Please do not click frequently！", //请不要频繁点击
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
      // if (this.replayboolen == true) {
      this.curren_index = index;
      this.dialogreplay = true;
      console.log(this.curren_index);
      // } else {
      //   return;
      // }
    },
    Accept() {
      //配置（对话框）
      console.log(this.replay[this.curren_index]);

      this.replay[this.curren_index].RecordChannelIndex = parseInt(
        this.PlayChannelIndex
      );
      this.replay[this.curren_index].RecordRXGain = parseInt(this.PlayTXGain);
      this.replay[this.curren_index].RecordRXFrequency = parseInt(
        this.PlayTXFrequency
      );
      this.dialogreplay = false;
    },
    close() {
      this.dialogreplay = false;
    },
    StartPlay() {
      //下发StartPlay API请求  多条下发
      var data = this.playData;
      var item;

      if (this.replayboolen == true) {
        this.replayboolen = false;

        //socket请求----
        var ws = new WebSocket("ws://192.168.1.203:9001");
        ws.onopen = function (e) {
          console.log(ws.readyState);
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
          console.log(e.data);
          if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
            that.$message.error("General error!"); //通用错误
          } else {
            var statu = JSON.parse(e.data).cmd.ResultCode;
            switch (statu) {
              case 0:
                that.$message({
                  message: "success", //成功
                  type: "success",
                });
                break;
              case 1:
                that.$message.error("file does not exist!"); //文件不存在
                break;
              case 2:
                that.$message({
                  message: "Card ID is already in use!", //板卡ID已被使用
                  type: "warning",
                });
                break;
              case 3:
                that.$message({
                  message: "Other failures!", //其他失败
                  type: "warning",
                });
                break;
            }
          }

          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };

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
            // this.replayboolen = true
          }, 4000);
        } else {
          this.$message({
            message: "Please do not click frequently！", //请不要频繁点击
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.replayboolen = false;
      } else {
        return;
      }
    },

    StartSynPlay() {
      //下发StartPlay API请求  组合多条数据在一个数组里面进行下发请求

      if (this.replayboolen == true) {
        var FileName;
        var data;
        setTimeout(() => {
          //定时器是因为vue框架默认的对你数组进行，立马存立马取，取不到，用定时器就可以取到
          data = this.playData;
          FileName = this.FileName;
          console.log(FileName);
          if (FileName[0].split("#")[0] != FileName[1].split("#")[0]) {
            this.$message({
              message: "Synchronous playback file selection error!", //同步回放文件选择错误
              type: "warning",
            });
          } else {
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
              if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                that.$message.error("General error!"); //通用错误
              } else {
                var statu = JSON.parse(e.data).cmd.ResultCode;
                switch (statu) {
                  case 0:
                    that.$message({
                      message: "success", //成功
                      type: "success",
                    });
                    break;
                  case 1:
                    that.$message.error("file does not exist!"); //文件不存在
                    break;
                  case 2:
                    that.$message({
                      message: "Card ID is already in use!", //板卡ID已被使用
                      type: "warning",
                    });
                    break;
                  case 3:
                    that.$message({
                      message: "Other failures!", //其他失败
                      type: "warning",
                    });
                    break;
                }
              }

              //关闭TCP连接
              ws.close();
              ws.onclose = function (e) {
                console.log(e);
              };
            };
          }
        }, 800);
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
            message: "Please do not click frequently！", //请不要频繁点击
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.replayboolen = false;
      } else {
        return;
      }
    },
    StopPlay() {
      //下发StartPlay API请求  单条数据下发

      //this.stopPlayData
      console.log(this.stopPlayData);
      var data = this.stopPlayData;
      var item;

      if (this.replayboolen == true) {
        this.replayboolen = false;
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
          if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
            that.$message.error("General error!"); //通用错误
          } else {
            var statu = JSON.parse(e.data).cmd.ResultCode;
            switch (statu) {
              case 0:
                that.$message({
                  message: "success", //成功
                  type: "success",
                });
                break;
              case 1:
                that.$message.error("file does not exist!"); //文件不存在
                break;
            }
          }

          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };

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
            message: "Please do not click frequently！", //请不要频繁点击
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.replayboolen = false;
      } else {
        return;
      }
    },

    StopSynPlay() {
      //下发StartPlay API请求  单条数据下发

      if (this.replayboolen == true) {
        var data;
        var FileName;
        setTimeout(() => {
          data = this.stopPlayData;
          FileName = this.FileName;
          console.log(FileName);
          if (FileName[0].split("#")[0] != FileName[1].split("#")[0]) {
            this.$message({
              message: "Synchronous playback file selection error!", //同步回放文件选择错误
              type: "warning",
            });
          } else {
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
              if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                that.$message.error("General error!"); //通用错误
              } else {
                var statu = JSON.parse(e.data).cmd.ResultCode;
                switch (statu) {
                  case 0:
                    that.$message({
                      message: "success", //成功
                      type: "success",
                    });
                    break;
                  case 1:
                    that.$message.error("file does not exist!"); //文件不存在
                    break;
                }
              }

              //关闭TCP连接
              ws.close();
              ws.onclose = function (e) {
                console.log(e);
              };
            };
          }
          //socket请求----
        }, 800);

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
            message: "Please do not click frequently！", //请不要频繁点击
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.replayboolen = false;
      } else {
        return;
      }
    },
    StopRecord() {
      //下发停止录制StopRecord请求  单条数据下发
      var data = this.stopRcordData;
      var item;
      console.log(data);

      if (this.replayboolen == true) {
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
          if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
            that.$message.error("General error!"); //通用错误
          } else {
            var statu = JSON.parse(e.data).cmd.ResultCode;
            switch (statu) {
              case 0:
                that.$message({
                  message: "success", //成功
                  type: "success",
                });
                break;
              case 1:
                that.$message.error("fail!"); //失败
                break;
            }
          }

          //关闭TCP连接
          ws.close();
          ws.onclose = function (e) {
            console.log(e);
          };
        };

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
            message: "Please do not click frequently！", //请不要频繁点击
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.replayboolen = false;
      } else {
        return;
      }
    },

    StopSynRecord() {
      //下发停止录制StopRecord请求  组合数据下发
      console.log(this.stopRcordData);

      var data;
      var FileName;
      if (this.replayboolen == true) {
        setTimeout(() => {
          data = this.stopRcordData;
          FileName = this.FileName;
          console.log(FileName);
          if (FileName[0].split("#")[0] != FileName[1].split("#")[0]) {
            this.$message({
              message: "Error selecting sync recording file!", //同步录制文件选择错误
              type: "warning",
            });
          } else {
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
              if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                that.$message.error("General error!"); //通用错误
              } else {
                var statu = JSON.parse(e.data).cmd.ResultCode;
                switch (statu) {
                  case 0:
                    that.$message({
                      message: "success", //成功
                      type: "success",
                    });
                    break;
                  case 1:
                    that.$message.error("fail!"); //失败
                    break;
                }
              }

              //关闭TCP连接
              ws.close();
              ws.onclose = function (e) {
                console.log(e);
              };
            };
          }

          //socket请求----
        }, 800);

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
            message: "Please do not click frequently！", //请不要频繁点击
            type: "warning",
          });
        }
        //提示不要频繁点击

        this.replayboolen = false;
      } else {
        return;
      }
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
  width: 140%;
  height: auto;
}
table th {
  height: 5rem;
  font-size: 1.3rem;
  border-bottom: 1px solid gainsboro;
}
table td {
  height: 5rem;
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

.replay >>> .el-dialog {
  width: 30%;
  box-shadow: 0 1px 3px rgb(0 0 0 / 5%);
}
.replay >>> .ui-widget-content {
  border: none;
  background: none;
}

@media screen and (min-width: 500px) and (max-width: 1297px) {
  table {
    width: 200%;
    height: auto;
  }
  .replay >>> .el-dialog {
    width: 40%;
  }
}
@media screen and (min-width: 800px) and (max-width: 1297px) {
  .replay >>> .el-dialog {
    width: 30%;
  }
}

@media screen and (min-width: 1356px) and (max-width: 1460px) {
  table {
    width: 160%;
    height: auto;
  }
}
@media screen and (min-width: 500px) and (max-width: 1356px) {
  table {
    width: 220%;
    height: auto;
  }
}
@media screen and (min-width: 899px) and (max-width: 1029px) {
  table {
    width: 260%;
    height: auto;
  }
}
@media screen and (min-width: 500px) and (max-width: 899px) {
  table {
    width: 290%;
    height: auto;
  }
  .replay >>> .el-dialog {
    width: 50%;
  }
}
@media screen and (min-width: 100px) and (max-width: 500px) {
  table {
    width: 300%;
    height: auto;
  }
  .replay >>> .el-dialog {
    width: 70%;
  }
}
</style>