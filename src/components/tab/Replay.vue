
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

          <th>Describe</th>
          <th>RF Port</th>
          <th>FileCurrentSize(GB)</th>
          <th>Center Frequency(MHz)</th>
          <th>Bits</th>
          <!-- <th>SampleRate(Hz)</th> -->
          <th>Band Width(MHz)</th>
          <!-- <th>RecordRXGain(Hz)</th> -->
          <th>More Info</th>
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

          <td class="td">
            <div @click="ShowDes(item)">{{ item.Describe }}</div>
          </td>
          <td class="td">{{ item.RecordChannelIndex }}</td>
          <td class="td">
            {{ (item.FileCurrentSize / 1000000000).toFixed(3) }}
          </td>
          <td class="td">
            {{ (item.RecordRXFrequency / 1000000).toFixed(3) }}
          </td>
          <td class="td">{{ item.BitNumber }}</td>
          <!-- <td class="td">{{ item.SampleRate }}</td> -->
          <td class="td">{{ item.RecordBandwidth / 1000000 }}</td>
          <!-- <td class="td">{{ item.RecordRXGain }}</td> -->
          <td class="td">
            <el-button
              type="warning"
              class="btn query"
              circle
              @click="ShowMore(item)"
              id="Showore"
              >···</el-button
            >
            <!-- <el-button type="warning" class="btn query" @click="ShowMore(item)"
              >View more information</el-button
            > -->
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

              <el-form-item label="PlayStartPos" :label-width="formLabelWidth">
                <el-input
                  autocomplete="off"
                  v-model="PlayStartPos"
                  placeholder="20"
                  min="0"
                  max="99"
                ></el-input>
              </el-form-item>

              <!-- <el-form-item
                label="PlayTXFrequency(Hz)"
                :label-width="formLabelWidth"
              >
                <el-input
                  autocomplete="off"
                  v-model="PlayTXFrequency"
                  placeholder="1575420000"
                ></el-input>
              </el-form-item> -->
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

    <p>
      RemainHarddisk Size: &nbsp;&nbsp;&nbsp;{{
        (RemainHarddiskSize / 1000000000).toFixed(3)
      }}
      GB &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
      &nbsp;&nbsp;&nbsp;&nbsp;<span
        style="color: rgb(245, 124, 0); font-size: 1.5rem"
        >Playback progress:&nbsp;<span>{{ playProgress }}%</span></span
      >
    </p>
    <p>
      RemainExHarddiskSize: &nbsp;&nbsp;&nbsp;{{
        (RemainExHarddiskSize / 1000000000).toFixed(3)
      }}
      GB
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
      <el-button class="btn query" @click="StartSynPlay"
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
import { formatDate } from "@/utils/formatDate";

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
      RemainHarddiskSize: 0, //用来存储磁盘大小
      RemainExHarddiskSize: 0, //用来存储外置磁盘大小
      IndexList: [], //用来控制只可以选中两个index不一样的复选框
      ChannelIndex: [], //板卡
      dialogreplay: false, //对话框是否显示

      Posbollen: false,
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
      PlayTXGain: 50,
      PlayStartPos: 0,

      curren_index: "", //获取index，取到当下点击的是哪一条数据
      playFileName: [],

      playProgress: 0,
      time: {
        nStartTime: 0,
        nEndTime: 0,
        FileTime: 0,
        setinterTime: "",
        boolen: false,
      },
    };
  },
  mounted() {
    //拖拽事件
    $(function () {
      $("#dialogreplay").draggable();
    });

    console.log(formatDate("hhmmss"));
  },

  beforeDestroy() {
    clearInterval(this.time.setinterTime);
    this.time.setinterTime = null;
  },
  methods: {
    //显示描述
    ShowDes(item) {
      var Describe = item.Describe;

      this.$alert(
        "<p><strong>Describe&nbsp;:&nbsp;</strong>" + Describe + "</p>",
        "View Describe",
        {
          confirmButtonText: "Close",
          dangerouslyUseHTMLString: true,
        }
      );
    },
    //显示更多
    ShowMore(item) {
      console.log(item);
      console.log(item.RecordRXGain);
      var save;
      var RecordRXGain = item.RecordRXGain;
      if (item.isUseExDisk == 0) {
        save = "Built in storage";
      } else {
        save = "External storage";
      }

      this.$alert(
        "<p><strong>RecordXRgain&nbsp;:&nbsp;</strong>" +
          RecordRXGain +
          "(dB)</p><p><strong>Storage location&nbsp;:&nbsp;</strong>" +
          save +
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
    getProgress() {
      var settime = null;
      // console.log(this.replay);
      var filename = this.replay.filter((item) => {
        return item.isRecording == 0 && item.isPlaying == 1;
      });
      this.playFileName = filename;
      // console.log(1234567);
      // console.log(this.playFileName);
      // console.log(this.playFileName[0].FileName); //.FileName当找不到正在回放的数据是，就会在报错，是因为没有找到，不会影响功能进行
      var fileTime = this.playFileName[0].FileName;
      // console.log(fileTime);
      var a = fileTime.split("_")[3];
      var b = fileTime.split("_")[5];
      var c = fileTime.split("_")[2];
      // console.log(a);
      // console.log(b);
      // console.log(c);
      // console.log(b - a);
      var time = b - a;
      console.log("文件时间为" + time);

      var that = this;
      this.time.setinterTime = setInterval(() => {
        that.time.nEndTime = formatDate("hhmmss");
        console.log("End为" + that.time.nEndTime);
        console.log("Start为" + that.time.nStartTime);
        console.log("差为" + (that.time.nEndTime - that.time.nStartTime));

        that.playProgress = (
          ((that.time.nEndTime - that.time.nStartTime) / time) *
          100
        ).toFixed(3);
        console.log("进度为" + that.playProgress);
        if (that.time.boolen == true) {
          that.time.boolen == false;
          console.log("bollen为" + that.time.boolen);
          clearInterval(that.time.setinterTime);
          that.playProgress = 0;
        }
      }, 3000);
    },
    //获取查询信息
    getReplay() {
      this.playFileName = [];
      //获取数据
      // console.log(this.replay);

      var replaying = this.replay.filter(function (item) {
        return item.isRecording == 0 && item.isPlaying == 1;
      });
      console.log(replaying);
      if (replaying.length == 0) {
        console.log(346780987654321);
        this.playProgress = 0;
        this.time.boolen = true;
      } else {
        this.time.boolen = false
        this.getProgress();
      }

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
          that.$message.error({
            message: "General error!",
            duration: 0,
            showClose: true,
          }); //通用错误
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
          that.$store.commit("getRecording", RecordData);
          if (RecordData.length > 0) {
            console.log(111);
            console.log(that.$store.state.Recording);
            for (var i = 0; i < RecordData.length; i++) {
              // console.log(RecordData[i].FileSize);
              // console.log(RecordData[i].FileCurrentSize);
              Availablespace =
                Allspace -
                (RecordData[i].FileSize - RecordData[i].FileCurrentSize);
              that.$store.commit("getAvailablespace", Availablespace);
              // console.log(Availablespace);
              // console.log(that.$store.state.Availablespace);
            }
          }
          // console.log(Allspace);
          // console.log(RecordData);
          // console.log(Availablespace);
          //that.getProgress(); //调用获取进度函数
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
        console.log(data);
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
            that.$message.error({
              message: "General error!",
              duration: 0,
              showClose: true,
            }); //通用错误
          } else {
            var statu = JSON.parse(e.data).cmd.FileInformations
              .PlayChannelIndexs;
            console.log(
              JSON.parse(e.data).cmd.FileInformations.PlayChannelIndexs
            );
            console.log(statu);
            if (statu.length == 0) {
              console.log("没有");
              that.$message({
                message: "No results found!", //未查询到结果
                type: "warning",
                duration: 0,
                showClose: true,
              });
            } else {
              console.log("有");
              for (var i = 0; i < statu.length; i++) {
                that.$message({
                  message: statu[i],
                  type: "success",
                  duration: 0,
                  showClose: true,
                });
              }
            }
            // switch (statu) {
            //   case 0:
            //     that.$message({
            //       message: statu,
            //       type: "success",
            //       duration: 0,
            //       showClose: true,
            //     });
            //     break;
            //   case 1:
            //     that.$message({
            //       message: statu,
            //       type: "success",
            //       duration: 0,
            //       showClose: true,
            //     });
            //     break;
            //   case (statu.length == 0):
            //     that.$message({
            //       message: "No results found!", //未查询到结果
            //       type: "warning",
            //       duration: 0,
            //       showClose: true,
            //     });
            //     break;
            // }
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

        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
      } else {
        return;
      }
    },
    Checkedreplay(e, item, index) {
      let ChannelIndex = item.RecordChannelIndex;
      if (e.target.checked == true) {
        this.IndexList.unshift(index);
        this.replayboolen = true;

        //要删除的数据操作
        this.removeData.push(item.FileName);
        // console.log(this.removeData);

        //要开始回放的数据操作
        if (this.Posbollen == true) {
          item.PlayStartPos = this.PlayStartPos;
          this.Posbollen == false;
        } else {
          item.PlayStartPos = 0;
          this.Posbollen == false;
        }
        this.playData.push({
          // RecordChannelIndex: item.RecordChannelIndex,
          FileName: item.FileName,
          PlayTXFrequency: parseInt(item.RecordRXFrequency),
          PlayTXGain: parseInt(item.RecordRXGain),
          PlayChannelIndex: parseInt(item.RecordChannelIndex),
          PlayStartPos: parseInt(item.PlayStartPos),
        });
        console.log(this.playData);

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
          PlayChannelIndexs: [],
        };
        // console.log(this.queryIndex);

        //组合下发判断是否选择的是一样的数据
        this.FileName.push(item.FileName);
        // console.log(this.FileName);
      } else {
        this.IndexList.splice(this.IndexList.indexOf(ChannelIndex), 1);
        this.playData.splice(this.IndexList.indexOf(ChannelIndex), 1);
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
                that.$message.error({
                  message: "General error!",
                  duration: 0,
                  showClose: true,
                }); //通用错误
              } else {
                var statu = JSON.parse(e.data).cmd.ResultCode;
                switch (statu) {
                  case 0:
                    that.$message({
                      message: "success", //成功
                      type: "success",
                      duration: 0,
                      showClose: true,
                    });
                    break;
                  case 1:
                    that.$message.error({
                      message: "file does not exist!",
                      duration: 0,
                      showClose: true,
                    }); //文件不存在
                    break;
                  case 2:
                    that.$message({
                      message: "The file is being recorded!", //文件正在录制
                      type: "warning",
                      duration: 0,
                      showClose: true,
                    });
                    break;
                  case 3:
                    that.$message({
                      message: "File playback in progress!", //文件正在回放
                      type: "warning",
                      duration: 0,
                      showClose: true,
                    });
                    break;
                }
              }

              //关闭TCP连接
              ws.close();
            };
            //socket请求----
          })
          .catch((err) => {
            this.replay.checked = false;
            console.log(err);
          });

        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
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
      this.Posbollen = true;
      //配置（对话框）
      console.log(this.replay[this.curren_index]);

      this.replay[this.curren_index].RecordChannelIndex = parseInt(
        this.PlayChannelIndex
      );
      this.replay[this.curren_index].RecordRXGain = parseInt(this.PlayTXGain);
      this.replay[this.curren_index].PlayStartPos = parseInt(this.PlayStartPos);
      console.log(this.replay[this.curren_index].PlayStartPos);
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

        // console.log(this.replay);
        var play = this.replay.filter((item) => {
          return item.isRecording == 0 && item.isPlaying == 1;
        });
        // console.log(play);
        // console.log(play.length);
        if (data.length > 1 || play.length != 0) {
          // console.log("回放全过程只允许操作一张卡");
          this.$message.error({
            message:
              "Only one card can be operated in the whole playback process!", //回放全过程只允许操作一张卡
            duration: 0,
            showClose: true,
          });
        } else {
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
            // console.log(e.data);
            if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
              that.$message.error({
                message: "General error!",
                duration: 0,
                showClose: true,
              }); //通用错误
            } else {
              var statu = JSON.parse(e.data).cmd.ResultCode;
              switch (statu) {
                case 0:
                  that.$message({
                    message: "success", //成功
                    type: "success",
                    duration: 0,
                    showClose: true,
                  });

                  //用来计算回放进度的参数
                  that.time.nStartTime = formatDate("hhmmss");
                  console.log(formatDate("hhmmss"));
                  that.getProgress();

                  break;
                case 1:
                  that.$message.error({
                    message: "file does not exist!",
                    duration: 0,
                    showClose: true,
                  }); //文件不存在
                  break;
                case 2:
                  that.$message({
                    message: "Card ID is already in use!", //板卡ID已被使用
                    type: "warning",
                    duration: 0,
                    showClose: true,
                  });
                  break;
                case 3:
                  that.$message({
                    message: "Other failures!", //其他失败
                    type: "warning",
                    duration: 0,
                    showClose: true,
                  });
                  break;
              }
            }

            //关闭TCP连接
            ws.close();
            ws.onclose = function (e) {
              // console.log(e);
            };
          };
          //socket请求----
        }

        //取消所有的复选框的勾选
        this.$refs.checkbox.map(function (item) {
          item.checked = false;
        });
        this.IndexList = [];
        //取消所有的复选框的勾选

        this.replayboolen = false;

        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
      } else {
        return;
      }
    },

    StartSynPlay() {
      //下发StartPlay API请求  组合多条数据在一个数组里面进行下发请求

      if (this.replayboolen == true) {
        var FileName;
        var data;
        if (this.playData.length > 2) {
          this.$message({
            message: "Select up to two files with different cards", //最多选择两条不同卡的文件
            type: "warning",
            duration: 0,
            showClose: true,
          });
        } else {
          // for (var i = 0; i < this.playData.length; i++) {
          if (
            this.playData[0].PlayChannelIndex ==
            this.playData[1].PlayChannelIndex
          ) {
            this.$message({
              message: "Please select two files of different cards", //请选择两条不同板卡的文件
              type: "warning",
              duration: 0,
              showClose: true,
            });
          }
          if (
            (this.playData[0].PlayChannelIndex == 0 &&
              this.playData[1].PlayChannelIndex == 1) ||
            (this.playData[0].PlayChannelIndex == 1 &&
              this.playData[1].PlayChannelIndex == 0)
          ) {
            setTimeout(() => {
              //定时器是因为vue框架默认的对你数组进行，立马存立马取，取不到，用定时器就可以取到
              data = this.playData;
              FileName = this.FileName;
              // console.log(FileName);
              if (FileName[0].split("#")[0] != FileName[1].split("#")[0]) {
                this.$message({
                  message: "Synchronous playback file selection error!", //同步回放文件选择错误
                  type: "warning",
                  duration: 0,
                  showClose: true,
                });
              } else {
                //socket请求----
                console.log(data);
                console.log(
                  JSON.stringify({
                    cmd: {
                      APIName: "StartPlay",
                      FileInformations: data,
                    },
                  })
                );
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
                  console.log(1234567789);
                };
                var that = this;
                ws.onmessage = function (e) {
                  if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                    that.$message.error({
                      message: "General error!",
                      duration: 0,
                      showClose: true,
                    }); //通用错误
                  } else {
                    var statu = JSON.parse(e.data).cmd.ResultCode;
                    switch (statu) {
                      case 0:
                        that.$message({
                          message: "success", //成功
                          type: "success",
                          duration: 0,
                          showClose: true,
                        });

                        //用来计算回放进度的参数
                        that.time.nStartTime = formatDate("hhmmss");
                        console.log(formatDate("hhmmss"));
                        that.getProgress();

                        break;
                      case 1:
                        that.$message.error({
                          message: "file does not exist!",
                          duration: 0,
                          showClose: true,
                        }); //文件不存在
                        break;
                      case 2:
                        that.$message({
                          message: "Card ID is already in use!", //板卡ID已被使用
                          type: "warning",
                          duration: 0,
                          showClose: true,
                        });
                        break;
                      case 3:
                        that.$message({
                          message: "Other failures!", //其他失败
                          type: "warning",
                          duration: 0,
                          showClose: true,
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
          }
          // }
        }

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

        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
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
            that.$message.error({
              message: "General error!",
              duration: 0,
              showClose: true,
            }); //通用错误
          } else {
            var statu = JSON.parse(e.data).cmd.ResultCode;
            switch (statu) {
              case 0:
                that.$message({
                  message: "success", //成功
                  type: "success",
                  duration: 0,
                  showClose: true,
                });
                break;
              case 1:
                that.$message.error({
                  message: "file does not exist!",
                  duration: 0,
                  showClose: true,
                }); //文件不存在
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
        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
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
              duration: 0,
              showClose: true,
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
                that.$message.error({
                  message: "General error!",
                  duration: 0,
                  showClose: true,
                }); //通用错误
              } else {
                var statu = JSON.parse(e.data).cmd.ResultCode;
                switch (statu) {
                  case 0:
                    that.$message({
                      message: "success", //成功
                      type: "success",
                      duration: 0,
                      showClose: true,
                    });
                    break;
                  case 1:
                    that.$message.error({
                      message: "file does not exist!",
                      duration: 0,
                      showClose: true,
                    }); //文件不存在
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
        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
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
            that.$message.error({
              message: "General error!",
              duration: 0,
              showClose: true,
            }); //通用错误
          } else {
            var statu = JSON.parse(e.data).cmd.ResultCode;
            switch (statu) {
              case 0:
                that.$message({
                  message: "success", //成功
                  type: "success",
                  duration: 0,
                  showClose: true,
                });
                break;
              case 1:
                that.$message.error({
                  message: "fail!",
                  duration: 0,
                  showClose: true,
                }); //失败
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
        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
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
              duration: 0,
              showClose: true,
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
                that.$message.error({
                  message: "General error!",
                  duration: 0,
                  showClose: true,
                }); //通用错误
              } else {
                var statu = JSON.parse(e.data).cmd.ResultCode;
                switch (statu) {
                  case 0:
                    that.$message({
                      message: "success", //成功
                      type: "success",
                      duration: 0,
                      showClose: true,
                    });
                    break;
                  case 1:
                    that.$message.error({
                      message: "fail!",
                      duration: 0,
                      showClose: true,
                    }); //失败
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
        setTimeout(() => {
          this.removeData = [];
          this.stopRcordData = [];
          this.playData = [];
          this.stopPlayData = [];
          this.FileName = [];
        }, 1000);
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
  overflow: scroll;
  margin: 0rem auto;
}
table {
  width: 100%;
  height: auto;
  /* background-color: pink */
  margin: 0rem auto;
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
table tr td:nth-child(4) {
  /* background-color: palegreen; */
  width: 10%;
  text-align: center;
}
table tr td:nth-child(4) div {
  width: 80%;
  /* background-color: pink; */
  color: rgb(37, 141, 222);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  cursor: pointer;
  margin-left: 30px;
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
#Showore {
  font-size: 1.7rem;
}
@media screen and (max-width: 1356px) and (min-width: 500px) {
  table {
    width: 165% !important;
    height: auto;
    /* background-color: pink; */
  }
}
@media screen and (min-width: 500px) and (max-width: 1297px) {
  table {
    width: 240%;
    height: auto;
    /* background-color: pink; */
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
    width: 170%;
    height: auto;
    /* background-color: pink; */
  }
}
@media screen and (min-width: 1000px) and (max-width: 1356px) {
  table {
    width: 250%;
    height: auto;
    /* background-color: pink; */
  }
}
@media screen and (min-width: 899px) and (max-width: 1029px) {
  table {
    width: 280%;
    height: auto;
    /* background-color: pink; */
  }
}
@media screen and (min-width: 500px) and (max-width: 899px) {
  table {
    width: 1000%;
    height: auto;
    /* background-color: pink; */
  }
  .replay >>> .el-dialog {
    width: 50%;
  }
}
@media screen and (min-width: 100px) and (max-width: 500px) {
  table {
    width: 700%;
    height: auto;
    /* background-color: pink; */
  }
  .replay >>> .el-dialog {
    width: 70%;
  }
}
</style>