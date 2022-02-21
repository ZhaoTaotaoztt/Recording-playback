<!--
 * @Author: your name
 * @Date: 2022-02-08 11:16:03
 * @LastEditTime: 2022-02-20 12:39:26
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
 * @FilePath: \admin\src\components\tab\replay.vue
-->
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
          <td>{{ item.FileInformations.FileName }}</td>

          <!-- 复选框在这 -->
          <td>
            <span
              class="status"
              :class="[
                item.FileInformations.isRecording == 1 &&
                item.FileInformations.isPlaying == 0
                  ? 'background-color'
                  : 'red',
                item.FileInformations.isRecording == 0 &&
                item.FileInformations.isPlaying == 1
                  ? 'background-color'
                  : 'green',
                item.FileInformations.isRecording == 0 &&
                item.FileInformations.isPlaying == 0
                  ? 'background-color'
                  : 'gray',
              ]"
            ></span
            >&nbsp;{{ item.FileInformations.isRecording
            }}{{ item.FileInformations.isPlaying }}
            <el-checkbox
              class="select"
              v-model="item.checked"
              @change="Checkedreplay(item.id, item.checked)"
            ></el-checkbox>
          </td>
          <!-- 复选框在这 -->

          <td>{{ item.FileInformations.RecordChannelIndex }}</td>
          <td>{{ item.FileInformations.FileSize }}</td>
          <td>{{ item.FileInformations.BitNumber }}</td>
          <td>{{ item.FileInformations.SampleRate }}</td>
          <td>{{ item.FileInformations.RecordBandwidth }}</td>
          <td>{{ item.FileInformations.RecordRXFrequency }}</td>
          <td>{{ item.FileInformations.RecordRXGain }}</td>
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
      <el-button class="btn query">Start Replay</el-button>
      <el-button class="btn query">Stop Replay</el-button>
      <el-button class="btn query">Stop Record</el-button>
    </p>
    <p>
      <el-button class="btn query">Start SynReplay</el-button>
      <el-button class="btn query">Stop SynReplay</el-button>
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
        <el-button type="primary" @click="playback">ACCEPT</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>

import $ from 'jquery'
import 'jquery-ui-dist/jquery-ui'
import 'jquery-ui-dist/jquery-ui.min.css'

export default {
  data() {
    return {
      replay: [],
      dialogreplay: false,
      replayid: [],
      replayboolen: false,
      formLabelWidth: "120px",
    };
  },
  methods: {
    //replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分replay部分
    getReplay() {
      //获取replay数据
      this.axios.get("/QueryFileInfo").then((res) => {
        this.replay = res.data;
        // console.log(this.replay);
      });
    },

    Checkedreplay(id, istrue) {
      //复选框选中的事件
      // console.log(id, istrue);
      if (istrue == true) {
        this.replayid.push(id);
        this.replayboolen = istrue;
      } else {
        this.replayboolen = istrue;
      }
    },
    Deletreplay() {
      //删除复选框选中的信息
      if (this.replayboolen == true) {
        this.$confirm("确定要删除这条信息吗？")
          .then((_) => {
            for (var i = 0; i < this.replayid.length; i++) {
              this.axios
                .delete("/QueryFileInfo/" + this.replayid[i])
                .then((res) => {
                  this.getReplay();
                  this.replayboolen = false;
                });
            }
          })
          .catch((err) => {
            this.replay.checked = false;
            console.log(err);
          });
      } else {
        return;
      }
    },

    playback() {
      //配置的回放
      this.dialogreplay = false;
    },
  },
};
</script>

<style scoped>
#table{
  width: 100%;
  height: auto;
  overflow-x:scroll;
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