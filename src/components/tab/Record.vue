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
          <th>RF Port</th>
          <!-- <th>Filesize(Byte)</th> -->
          <th>Center Frequency(MHz)</th>
          <th>Bits</th>
          <!-- <th>SampleRate(Hz)</th> -->
          <th>Band Width(MHz)</th>
          <th>RXgain(dB)</th>
          <th>More Info</th>
        </tr>
        <tr v-for="(item, index) in record" :key="index">
          <!-- 复选框在这 -->
          <td>{{ item.id }}</td>
          <td>
            <div class="des" @click="ShowDes(item)">{{ item.Describe }}</div>
            &nbsp;
            <input
              type="checkbox"
              @change="Checkedrecord($event, item)"
              class="select"
              ref="checkbox"
            />
          </td>
          <!-- 复选框在这 -->

          <td>{{ item.RecordChannelIndex }}</td>
          <!-- <td>
            <input
              type="text"
              class="text"
              v-model="item.FileSize"
              @blur="Blur"
            />
          </td> -->
          <td>
            <input
              type="text"
              class="text"
              v-model="item.RecordRXFrequency"
              @blur="Blur"
            />
          </td>
          <td>
            <input
              type="text"
              class="text"
              v-model="item.BitNumber"
              @blur="Blur"
            />
          </td>
          <!-- <td>
            <input
              type="text"
              class="text"
              v-model="item.SampleRate"
              @blur="Blur"
            />
          </td> -->
          <td>
            <input
              type="text"
              class="text"
              v-model="item.RecordBandwidth"
              @blur="Blur"
            />
          </td>
          <td>
            <input
              type="text"
              class="text"
              v-model="item.RecordRXGain"
              @blur="Blur"
            />
          </td>
          <td class="td">
            <el-button
              type="warning"
              class="btn query"
              circle
              @click="ShowMore(item)"
              id="Showore"
              >···</el-button
            >
          </td>
        </tr>
      </table>
    </div>

    <p>
      <el-button id="btn" @click="StartRecord" v-preventReClick="10000"
        >Start Record</el-button
      >
      <el-button id="btn" @click="StartSynRecord" v-preventReClick="10000"
        >Start SynRecord</el-button
      >
    </p>

    <p>
      <a href="" id="a">click here to download your file</a>
      <!-- //导出文件 -->
      <el-button @click="download(exportdata, 'file.txt', 'text/plain/utf-8')">
        Export profile
      </el-button>
      <!-- exportdata是需要导出的数据；file.txt是到处时候的文件名字；text/plain/utf-8导出时的格式 -->
      <!-- //导入文件 -->
      <el-button id="father">
        Import profile
        <input type="file" @change="showFile($event)" id="child"
      /></el-button>
    </p>

    <!-- 嵌套的表单 -->
    <el-dialog
      title="新建录制配置"
      id="dialogrecord"
      :visible.sync="dialogrecord"
      :before-close="close"
      :modal="modal"
      class="ui-widget-content"
    >
      <el-form>
        <el-form-item label="ID" :label-width="formLabelWidth">
          <el-input
            type="number"
            v-model="recordform.id"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="RF Port" :label-width="formLabelWidth">
          <el-select
            v-model="recordform.RecordChannelIndex"
            type="number"
            class="select"
          >
            <el-option label="0" value="0"></el-option>
            <el-option label="1" value="1"></el-option>
          </el-select>
        </el-form-item>
        <!-- 
        <el-form-item label="Filesize(Byte)" :label-width="formLabelWidth">
          <el-input
            type="number"
            v-model="recordform.FileSize"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item> -->

        <el-form-item label="BitNum" :label-width="formLabelWidth">
          <el-select
            v-model="recordform.BitNumber"
            type="number"
            class="select"
          >
            <!-- <el-option label="2" value="2"></el-option> -->
            <!-- <el-option label="4" value="4"></el-option> -->
            <el-option label="8" value="8"></el-option>
            <el-option label="16" value="16"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item
          label="RecordBandWidth(MHz)"
          :label-width="formLabelWidth"
        >
          <el-select
            v-model="recordform.RecordBandwidth"
            :label="20"
            type="number"
            class="select"
          >
            <el-option label="8" value="8"></el-option>
            <el-option label="15" value="15"></el-option>
            <el-option label="25" value="25"></el-option>
            <el-option label="50" value="50"></el-option>
            <el-option label="100" value="100"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="Frequency(MHz)" :label-width="formLabelWidth">
          <!-- 第一个下拉框 -->
          <el-select
            v-model="frequencyForm.title"
            :label="20"
            type="number"
            class="select"
            @change="changeSelect($event)"
            placeholder="Please select"
          >
            <el-option
              v-for="(item, index) in frequencyTitle"
              :key="index"
              :label="item"
              :value="item"
            ></el-option>
          </el-select>
          &nbsp;&nbsp;&nbsp;&nbsp;
          <!-- 第二个下拉框 -->
          <el-select
            v-model="frequencyForm.data"
            :label="20"
            type="number"
            class="select"
            placeholder="Please select"
            @change="getfrenquencyData($event)"
          >
            <el-option
              v-for="(item, index) in frequencydataList"
              :key="index"
              :label="item.title"
              :value="item.data"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label=" " :label-width="formLabelWidth">
          <!-- 第三个输入框 -->
          <el-input
            type="number"
            autocomplete="off"
            suffix-icon="xxxx"
            id="edit"
            :disabled="editDisabled"
            v-model="recordform.RecordRXFrequency"
          ></el-input>
        </el-form-item>

        <!-- <el-form-item
          label="RecordFrequency(MHz)"
          :label-width="formLabelWidth"
        >
          <el-input
            type="number"
            v-model="recordform.RecordRXFrequency"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item> -->

        <el-form-item label="RecordXRgain(dB)" :label-width="formLabelWidth">
          <el-input
            type="number"
            v-model="recordform.RecordRXGain"
            autocomplete="off"
            suffix-icon="xxxx"
          ></el-input>
        </el-form-item>

        <el-form-item label="isUseExDisk" :label-width="formLabelWidth">
          <el-select
            v-model="recordform.isUseExDisk"
            placeholder="Please select"
            type="number"
            class="select"
          >
            <el-option label="YES" value="1"></el-option>
            <el-option label="NO" value="0"></el-option>
          </el-select>
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
import $ from "jquery";
import "jquery-ui-dist/jquery-ui";
import "jquery-ui-dist/jquery-ui.min.css";

export default {
  data() {
    return {
      ws: "192.168.1.75", //ip地址

      record: [
        {
          RecordChannelIndex: 1,
          BitNumber: 16,
          // SampleRate: 122880000,
          RecordBandwidth: 15,
          RecordRXFrequency: 1575.42,
          RecordRXGain: 40,
          id: 1,
          Describe: "GPS,Shenzhen Skyworth Industrial Park",
          isUseExDisk: 0,
        },
        {
          RecordChannelIndex: 0,
          BitNumber: 8,
          // SampleRate: 122880000,
          RecordBandwidth: 15,
          RecordRXFrequency: 1575.42,
          RecordRXGain: 40,
          id: 2,
          Describe: "GPS,Shenzhen Skyworth Industrial Park",
          isUseExDisk: 0,
        },
      ],

      exportdata: "", //导出的数据，所用位置在html中
      modal: false, //不要蒙层
      ChannelIndex: [], //板卡
      RemainHarddiskSize: 0, //用来存储磁盘大小
      IndexList: [], //用来控制只可以选中两个index不一样的复选框
      Availablespace: "", //剩余空间

      removeData: [], //需要删除的数据
      RecordData: [], //需要录制的数据

      recordboolen: false, //用来是否允许点击按钮的布尔值

      isclick: true, //用来判断提示不要频繁点击的布尔值

      dialogrecord: false, //对话框是否显示

      recordform: {
        //配置录制的数据的对话框所绑定的数据
        id: 1,
        RecordChannelIndex: 0,
        FileSize: 1000000000,
        BitNumber: 8,
        // SampleRate: 122880000,
        RecordBandwidth: 15,
        RecordRXFrequency: "", //1575.42
        RecordRXGain: 40,
        Describe: "",
        isUseExDisk: "0",
      },

      formLabelWidth: "175px", //控制对话框的长度

      //gnss频率的名字
      frequencyTitle: [
        "GPS",
        "GLONASS",
        "Galileo",
        "BeiDou",
        "NAVIC",
        "SBAS",
        "QZSS",
        "UserCustom",
      ],

      //gnss频率的名字 对应的详细数据
      frequencyData: {
        GPS: [
          { title: "L1C/A:1575.42", data: 1575.42 },
          { title: "L1C:1575.42", data: 1575.42 },
          { title: "L2C:1227.6", data: 1227.6 },
          { title: "L2P:1227.6", data: 1227.6 },
          { title: "L5:1176.45", data: 1176.45 },
        ],
        GLONASS: [
          { title: "L1C/A:1598.0625~1609.3125", data: 1598.0625 },
          { title: "L2C:1242.9375~1251.6875", data: 1242.9375 },
          { title: "L2P:1242.9375~1251.6875", data: 1242.9375 },
          { title: "L3 OC:1202.025", data: 1202.025 },
        ],
        Galileo: [
          { title: "E1:1575.42", data: 1575.42 },
          { title: "E5a:1176.45", data: 1176.45 },
          { title: "E5b:1207.14", data: 1207.14 },
          { title: "E5 AltBOC:1191.795", data: 1191.795 },
          { title: "E6:1278.75", data: 1278.75 },
        ],
        BeiDou: [
          { title: "B1l:1561.098", data: 1561.098 },
          { title: "B2l:1207.14", data: 1207.14 },
          { title: "B3l:1268.52", data: 1268.52 },
          { title: "B1C:1575.42", data: 1575.42 },
          { title: "B2a:1176.45", data: 1176.45 },
          { title: "B2b:1207.14", data: 1207.14 },
        ],
        NAVIC: [{ title: "L5:1176.45", data: 1176.45 }],
        SBAS: [
          { title: "L1:1575.42", data: 1575.42 },
          { title: "L5:1176.45", data: 1176.45 },
        ],
        QZSS: [
          { title: "L1C/A:1575.42", data: 1575.42 },
          { title: "L1C:1575.42", data: 1575.42 },
          { title: "L1S:1575.42", data: 1575.42 },
          { title: "L2C:1227.6", data: 1227.6 },
          { title: "L5:1176.45", data: 1176.45 },
          { title: "L6:1278.75", data: 1278.75 },
        ],
      },
      frequencydataList: [], //用来判断联动下拉框名字是否相同，相同的把对应的详细数据装在这个空数组里面，第二级下拉框绑定这个数组就可以实现联动了

      //用来获取一级下拉框，之后再显示二级下拉框
      frequencyForm: {
        title: "",
        data: "",
      },
      editDisabled: true, //用来控制频率的输入框是否可编辑
    };
  },
  created() {
    //获取本地存储里面的数据在created等函数里面，不为空的时候再取值，这样不刷新的时候也可以去得到
    var data = JSON.parse(window.localStorage.getItem("recordData"));
    if (data != null) {
      this.exportdata = JSON.stringify(data);
    }
    // console.log(this.exportdata);

    this.ChannelIndex = this.$store.state.ChannelIndex;

    //获取本地存储里面的数据在created等函数里面，不为空的时候再取值，这样不刷新的时候也可以去得到
    var cache = JSON.parse(window.localStorage.getItem("recordData"));
    if (cache != null) {
      this.record = cache;
    }

    this.record = this.record.sort((a, b) => a.id - b.id); //进行排序

    //对话框拖拽事件
    $(function () {
      $("#dialogrecord").draggable();
    });

    this.getRemainHarddiskSize();

    //获取IP地址
    this.ws = this.$store.state.ws;
    console.log(this.ws);
  },

  methods: {
    //联动的第一个下拉框事件
    changeSelect(event) {
      console.log(event);
      if (event !== "UserCustom" && event !== "GLONASS") {
        console.log("不可以编辑");
        console.log(this.editDisabled);
        this.editDisabled = true;
      } else {
        console.log("需要编辑");
        console.log(this.editDisabled);
        this.editDisabled = false;
      }
      // 清空具体频率的内容
      this.frequencyForm.data = "";

      // 遍历频率的名字下拉选项数组
      for (const k in this.frequencyTitle) {
        if (this.frequencyForm.title === this.frequencyTitle[k]) {
          this.frequencydataList = this.frequencyData[this.frequencyForm.title];
        }
      }
    },

    //联动的第二个下拉框事件
    getfrenquencyData(event) {
      this.recordform.RecordRXFrequency = event;
      console.log(event);
    },

    //数据下载为txt文件
    download(text, name, type) {
      console.log(text);
      var a = document.getElementById("a");
      var file = new Blob([text], {
        type: type,
      });
      a.href = URL.createObjectURL(file);
      a.download = name;
      a.dispatchEvent(
        new MouseEvent("click", {
          bubbles: false,
          cancelable: true,
        })
      );
    },

    //读取.txt文件里面的内容
    showFile(input) {
      //return false;
      if (window.FileReader) {
        var file = input.target.files[0];
        var reader = new FileReader();

        reader.readAsText(file, "GB2312");
        reader.onload = (res) => {
          //显示文件
          console.log(1111);
          console.log(res); //打印
          var result = res.result;
          console.log(res.target.result);
          this.record = JSON.parse(res.target.result);
          //存数据
          window.localStorage.setItem(
            "recordData",
            JSON.stringify(this.record)
          );
        };
      } else {
        alert("FileReader Not supported by your browser!");
      }
    },

    //获取磁盘大小，用来作为下发录制的参数
    getRemainHarddiskSize() {
      //socket请求----
      var ws = new WebSocket("ws://" + this.ws + ":9001");
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
        if (JSON.parse(e.data).APIName == "GenericErr") {
          that.$message.error("通用错误!");
        } else {
          var size = JSON.parse(e.data).cmd.RemainHarddiskSize;
          that.RemainHarddiskSize = parseInt(size) - 6000000000;
          console.log(size);
          console.log(that.RemainHarddiskSize);
        }
        //关闭socket连接
        ws.close();
        ws.onclose = function (e) {
          // console.log(e);
        };
      };
      //socket请求----
    },

    //显示描述信息
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

    //显示更多信息
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
        "<p><strong>Storage location&nbsp;:&nbsp;</strong>" + save + "</p>",
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

    //显示对话框
    Showdialog() {
      this.dialogrecord = true;
      this.ChannelIndex = this.$store.state.ChannelIndex;
      console.log(this.ChannelIndex);
    },

    //关闭对话框
    close() {
      this.dialogrecord = false;
    },

    //对话框的确认按钮
    addRecordData(id) {
      this.editDisabled = true;
      //添加新的数据到record
      this.dialogrecord = false;
      this.recordform = {
        id: parseInt(this.recordform.id),
        RecordChannelIndex: parseInt(this.recordform.RecordChannelIndex),
        FileSize: parseInt(this.recordform.FileSize),
        BitNumber: parseInt(this.recordform.BitNumber),
        SampleRate: parseInt(this.recordform.SampleRate),
        RecordBandwidth: parseInt(this.recordform.RecordBandwidth),
        RecordRXFrequency: parseFloat(this.recordform.RecordRXFrequency),
        RecordRXGain: parseInt(this.recordform.RecordRXGain),
        Describe: this.recordform.Describe,
        isUseExDisk: parseInt(this.recordform.isUseExDisk),
      };
      console.log(this.recordform);

      //用来查找数组里面有没有重复的
      function findElem(arrayToSearch, attr, val) {
        for (var i = 0; i < arrayToSearch.length; i++) {
          if (arrayToSearch[i][attr] == val) {
            return i;
          }
        }
        return -1;
      }

      if (this.record.length > 150) {
        this.$message({
          message: "Up to 150 rows can be configured！", //最多可配置150行
          type: "warning",
        });
      } else {
        if (findElem(this.record, "id", id) !== -1) {
          this.$message({
            message: "ID already exists, please re-enter！", //id 已经存在，请重新输入
            type: "warning",
            duration: 0,
            showClose: true,
          });
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
            isUseExDisk: "0",
          };
        } else {
          console.log(111);
          console.log(this.recordform);
          var commont = this.record.findIndex((item) => {
            return (
              item.FileSize == this.recordform.FileSize &&
              item.BitNumber == this.recordform.BitNumber &&
              item.SampleRate == this.recordform.SampleRate &&
              item.RecordBandwidth == this.recordform.RecordBandwidth &&
              item.RecordRXFrequency == this.recordform.RecordRXFrequency &&
              item.RecordRXGain == this.recordform.RecordRXGain
            );
          });
          console.log(commont);
          if (commont !== -1) {
            this.$message({
              message: "Duplicate recording file configuration！", //录制文件配置重复
              type: "warning",
              duration: 0,
              showClose: true,
            });
          } else {
            this.record.push(this.recordform);
            //加进去的按照顺序的往下排序，始终按照12345这样的顺序排下去
            this.record.forEach((item, index) => {
              // console.log(item);
              item.id = index + 1;
              // console.log(index);
            });
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
              isUseExDisk: "0",
            };
            //添加的新数据存储到localstroge
            window.localStorage.setItem(
              "recordData",
              JSON.stringify(this.record)
            );
            // console.log(111);
            console.log(window.localStorage.getItem("recordData"));
            this.record = this.record.sort((a, b) => a.id - b.id); //进行排序

            //导出的数据加进去的时候就需要存储了
            var data = JSON.parse(window.localStorage.getItem("recordData"));
            this.exportdata = JSON.stringify(data);
          }
        }
      }
    },

    //多选框选中的事件
    Checkedrecord(e, item) {
      this.getRemainHarddiskSize();
      let index = item.RecordChannelIndex;
      if (e.target.checked == true) {
        this.IndexList.unshift(index);
        this.recordboolen = true;

        //需要删除的数据
        this.removeData.push(item.id);

        //需要录制的数据
        this.RecordData.push({
          FileName: "NA",
          FileSize: parseInt(this.RemainHarddiskSize),
          BitNumber: parseInt(item.BitNumber),
          // SampleRate: parseInt(item.SampleRate),
          RecordBandwidth: parseInt(item.RecordBandwidth) * 1000000,
          RecordRXFrequency: parseFloat(item.RecordRXFrequency) * 1000000,
          RecordRXGain: parseInt(item.RecordRXGain),
          RecordChannelIndex: parseInt(item.RecordChannelIndex),
          Describe: item.Describe,
          isUseExDisk: parseInt(item.isUseExDisk),
        });
        //录制的文件大小，总大小减去6G处以数组的长度
        for (var i = 0; i < this.RecordData.length; i++) {
          this.RecordData[i].FileSize = parseInt(
            this.RemainHarddiskSize / this.RecordData.length
          );
        }
        console.log(this.RecordData);
        console.log(this.RecordData[0].FileSize);
      } else {
        this.IndexList.splice(this.IndexList.indexOf(index), 1);
        this.RecordData.splice(this.IndexList.indexOf(index), 1);
        this.removeData.splice(this.IndexList.indexOf(index), 1);
        this.recordboolen = false;
        // console.log(this.RecordData);
      }
    },

    //删除信息事件
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
        this.$confirm("Are you sure you want to delete?", {
          //确定要删除吗?
          confirmButtonText: "Determine",
          cancelButtonText: "Cancel",
        })
          .then((_) => {
            //console.log(ids);return false;
            bantchDelete(arr, ids); //进行删除
            // this.record = bantchDelete(arr, ids);

            this.removeData = []; //删除以后把数组清空

            this.recordboolen = false;
            this.RecordData = [];

            //删除的按照顺序的往下排序，始终按照12345这样的顺序排下去
            this.record.forEach((item, index) => {
              item.id = index + 1;
            });

            window.localStorage.setItem("recordData", JSON.stringify(arr)); //存储覆盖

            setTimeout(() => {
              this.RecordData = [];
              this.removeData;
            }, 1000);
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

    //页面中的输入框失去焦点事件
    Blur() {
      //存数据
      console.log(111);
      window.localStorage.setItem("recordData", JSON.stringify(this.record));

      //取数据
      this.record = JSON.parse(window.localStorage.getItem("recordData"));
    },

    //单独下发录制文件
    StartRecord() {
      //获取IP地址
      this.ws = this.$store.state.ws;
      console.log(this.ws);

      if (this.recordboolen == true) {
        this.Availablespace = this.$store.state.Availablespace;
        console.log(this.$store.state.Recording);
        // console.log(this.Availablespace);
        console.log(11);
        if (parseInt(this.Availablespace) < 5220000000) {
          this.$message({
            message: "Insufficient disk space！！！", //磁盘空间不足
            type: "warning",
            duration: 0,
            showClose: true,
          });
          this.RecordData = [];
        }
        if (this.RecordData.length != 1) {
          console.log("单独录制的文件只允许操作一个");
          this.$message({
            message:
              "Only one operation is allowed for a separately recorded file！！！", //已经有文件正在录制
            type: "warning",
            duration: 0,
            showClose: true,
          });
        }
        if (this.RecordData.length == 1) {
          if (this.$store.state.Recording.length > 0) {
            this.$message({
              message: "There are already files recording！！！", //已经有文件正在录制
              type: "warning",
              duration: 0,
              showClose: true,
            });
            var Recording = [];
            this.$store.commit("getRecording", Recording);
            //取消所有的复选框的勾选
          } else {
            var data = this.RecordData;
            // console.log(data);

            //存储内置数据
            var int = data.filter(function (e) {
              return e.isUseExDisk == 0;
            });

            //存储外置数据
            var out = data.filter(function (e) {
              return e.isUseExDisk == 1;
            });
            var that = this;
            var outboolen = false;

            // //socket请求----
            var ws = new WebSocket("ws://" + this.ws + ":9001");

            //内置存储内置存储内置存储内置存储内置存储内置存储内置存储内置存储
            if (int.length > 0) {
              ws.onopen = function (e) {
                // console.log(int);
                //所有数据都要使用
                for (var i = 0; i < int.length; i++) {
                  console.log(
                    JSON.stringify({
                      cmd: {
                        APIName: "AddFileInfo",
                        FileInformations: [int[i]],
                      },
                    })
                  );
                  ws.send(
                    JSON.stringify({
                      cmd: {
                        APIName: "AddFileInfo",
                        FileInformations: [int[i]],
                      },
                    })
                  );
                }
              };
              ws.onmessage = function (e) {
                if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                  that.$message.error({
                    message: "General error!",
                    duration: 0,
                    showClose: true,
                  }); //通用错误
                } else {
                  // console.log(JSON.parse(e.data).cmd.ResultCode);
                  var staut = parseInt(JSON.parse(e.data).cmd.ResultCode);
                  // console.log(this.boardinfo);
                  switch (staut) {
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
                        message: "Recording failed!",
                        duration: 0,
                        showClose: true,
                      }); //录制失败
                      break;
                    case 2:
                      that.$message({
                        message:
                          "The card channel ID has been used and can be modified or re added in the edit box!", //板卡板卡通道ID已经被使用，可在编辑框修改或者重新添加
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
            }
            //内置存储内置存储内置存储内置存储内置存储内置存储内置存储内置存储

            //外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储
            if (out.length > 0) {
              console.log(out);
              ws.onopen = function (e) {
                //所有数据都要使用
                // for (var i = 0; i < out.length; i++) {
                ws.send(
                  JSON.stringify({
                    cmd: {
                      APIName: "QueryExDisk",
                    },
                  })
                );
              };
              // }
              ws.onmessage = function (e) {
                console.log(JSON.parse(e.data).cmd.ResultCode);
                console.log(JSON.parse(e.data).cmd.ResultCode);
                if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                  that.$message.error({
                    message: "General error!",
                    duration: 0,
                    showClose: true,
                  }); //通用错误
                }
                if (JSON.parse(e.data).cmd.ResultCode == 0) {
                  that.$message({
                    message: "The extended hard disk does not exist!", //扩展硬盘不存在
                    type: "warning",
                    duration: 0,
                    showClose: true,
                  });
                }
                if (JSON.parse(e.data).cmd.ResultCode == 1) {
                  outboolen = true;
                  console.log(12323435);
                  for (var i = 0; i < out.length; i++) {
                    console.log(out[i]);
                    console.log(
                      JSON.stringify({
                        cmd: {
                          APIName: "AddFileInfo",
                          FileInformations: [out[i]],
                        },
                      })
                    );

                    ws.send(
                      JSON.stringify({
                        cmd: {
                          APIName: "AddFileInfo",
                          FileInformations: [out[i]],
                        },
                      })
                    );
                  }
                  ws.onmessage = function (e) {
                    console.log(11111);
                    console.log(JSON.parse(e.data).cmd.ResultCode);
                  };
                }
                //关闭TCP连接
                ws.close();
                
              };
            }

            if (outboolen == true) {
              console.log(outboolen);
              console.log("外置配置");
              ws.onmessage = function (e) {
                if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                  that.$message.error({
                    message: "General error!",
                    duration: 0,
                    showClose: true,
                  }); //通用错误
                } else {
                  console.log(111);
                  console.log(JSON.parse(e.data).cmd.ResultCode);
                  var staut = parseInt(JSON.parse(e.data).cmd.ResultCode);
                  // console.log(this.boardinfo);
                  switch (staut) {
                    case 0:
                      that.$message({
                        message: "success", //成功
                        type: "success",
                        duration: 0,
                        showClose: true,
                      });
                      break;
                    case 1:
                      that.$message.error("Recording failed!"); //录制失败
                      break;
                    case 2:
                      that.$message({
                        message:
                          "The card channel ID has been used and can be modified or re added in the edit box!", //板卡板卡通道ID已经被使用，可在编辑框修改或者重新添加
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
              outboolen = false;
            }
            //外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储
          }
        }
        //socket请求----

        //取消所有的复选框的勾选
        this.$refs.checkbox.map(function (item) {
          item.checked = false;
        });
        this.IndexList = [];
        this.RecordData = [];
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
            duration: 0,
            showClose: true,
          });
        }
        //提示不要频繁点击

        this.recordboolen = false;
        //操作数据清空
        setTimeout(() => {
          this.RecordData = [];
          this.removeData = [];
        }, 1000);
      } else {
        return;
      }
    },

    //组合下发录制文件
    StartSynRecord() {
      //获取IP地址
      this.ws = this.$store.state.ws;
      console.log(this.ws);

      if (this.recordboolen == true) {
        this.Availablespace = this.$store.state.Availablespace;
        if (parseInt(this.Availablespace) < 5220000000) {
          this.$message({
            message: "Insufficient disk space！！！", //磁盘空间不足
            type: "warning",
            duration: 0,
            showClose: true,
          });
        } else {
          var data;
          if (this.RecordData.length != 2) {
            this.$message({
              message: "Please select two files with different cards", //请选择两条不同卡的文件
              type: "warning",
              duration: 0,
              showClose: true,
            });
            //取消所有的复选框的勾选
            this.$refs.checkbox.map(function (item) {
              item.checked = false;
            });
            this.IndexList = [];
            //取消所有的复选框的勾选

            //操作数据清空
            setTimeout(() => {
              this.RecordData = [];
              this.removeData;
            }, 1000);
          }
          if (this.RecordData.length == 2) {
            // for (var i = 0; i < this.RecordData.length; i++) {
            // console.log(this.RecordData[0].RecordChannelIndex);
            // console.log(this.RecordData[1].RecordChannelIndex);
            if (
              this.RecordData[0].RecordChannelIndex ==
              this.RecordData[1].RecordChannelIndex
            ) {
              // console.log(12345);
              this.$message({
                message: "Please select two files of different cards", //请选择两条不同板卡的文件
                type: "warning",
                duration: 0,
                showClose: true,
              });
            }
            if (
              (this.RecordData[0].RecordChannelIndex == 0 &&
                this.RecordData[1].RecordChannelIndex == 1) ||
              (this.RecordData[0].RecordChannelIndex == 1 &&
                this.RecordData[1].RecordChannelIndex == 0)
            ) {
              setTimeout(() => {
                data = this.RecordData;
                // console.log(data);
                // console.log(
                //   JSON.stringify({
                //     cmd: {
                //       APIName: "AddFileInfo",
                //       FileInformations: data,
                //     },
                //   })
                // );
                //存储内置数据
                var int = data.filter(function (i) {
                  return i.isUseExDisk == 0;
                });
                //存储外置数据
                var out = data.filter(function (i) {
                  return i.isUseExDisk == 1;
                });
                var that = this;
                var outboolen = false;

                console.log(1111);
                console.log(int);
                // //socket请求----
                var ws = new WebSocket("ws://" + this.ws + ":9001");
                for (var i = 0; i < data.length; i++) {
                  // console.log(data[i].isUseExDisk);

                  //内置存储内置存储内置存储内置存储内置存储内置存储内置存储内置存储内置存储
                  if (data[0].isUseExDisk == 0 && data[1].isUseExDisk == 0) {
                    console.log(
                      JSON.stringify({
                        cmd: {
                          APIName: "AddFileInfo",
                          FileInformations: int,
                        },
                      })
                    );
                    ws.onopen = function (e) {
                      ws.send(
                        JSON.stringify({
                          cmd: {
                            APIName: "AddFileInfo",
                            FileInformations: int,
                          },
                        })
                      );
                    };

                    ws.onmessage = function (e) {
                      if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                        that.$message.error({
                          message: "General error!",
                          duration: 0,
                          showClose: true,
                        }); //通用错误
                      } else {
                        console.log(JSON.parse(e.data).cmd.ResultCode);
                        var staut = parseInt(JSON.parse(e.data).cmd.ResultCode);
                        // console.log(this.boardinfo);
                        switch (staut) {
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
                              message: "Recording failed!",
                              duration: 0,
                              showClose: true,
                            }); //录制失败
                            break;
                          case 2:
                            that.$message({
                              message:
                                "The card channel ID has been used and can be modified or re added in the edit box!", //板卡板卡通道ID已经被使用，可在编辑框修改或者重新添加
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
                  }
                  //内置存储内置存储内置存储内置存储内置存储内置存储内置存储内置存储内置存储

                  //外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储
                  if (data[0].isUseExDisk == 1 && data[1].isUseExDisk == 1) {
                    ws.onopen = function (e) {
                      ws.send(
                        JSON.stringify({
                          cmd: {
                            APIName: "QueryExDisk",
                          },
                        })
                      );
                    };

                    ws.onmessage = function (e) {
                      // console.log(JSON.parse(e.data).cmd.ResultCode);
                      if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                        that.$message.error({
                          message: "General error!",
                          duration: 0,
                          showClose: true,
                        }); //通用错误
                      }
                      if (JSON.parse(e.data).cmd.ResultCode == 0) {
                        that.$message({
                          message: "The extended hard disk does not exist!", //扩展硬盘不存在
                          type: "warning",
                          duration: 0,
                          showClose: true,
                        });
                      }
                      if (JSON.parse(e.data).cmd.ResultCode == 1) {
                        outboolen = true;
                        ws.send(
                          JSON.stringify({
                            cmd: {
                              APIName: "AddFileInfo",
                              FileInformations: out,
                            },
                          })
                        );
                      }

                      //关闭TCP连接
                      ws.close();
                    };
                  }
                  //外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储外置存储

                  if (
                    (data[0].isUseExDisk == 0 && data[1].isUseExDisk == 1) ||
                    (data[0].isUseExDisk == 1 && data[1].isUseExDisk == 0)
                  ) {
                    this.$message({
                      message:
                        "Please keep the storage location of combined recording consistent！", //组合录制的存储位置请保持一致！
                      type: "warning",
                      duration: 0,
                      showClose: true,
                    });
                  }

                  //外置存储外置存储外置
                  if (outboolen == true) {
                    console.log("外置配置");
                    ws.onmessage = function (e) {
                      if (JSON.parse(e.data).cmd.APIName == "GenericErr") {
                        that.$message.error({
                          message: "General error!",
                          duration: 0,
                          showClose: true,
                        }); //通用错误
                      } else {
                        console.log(JSON.parse(e.data).cmd.ResultCode);
                        var staut = parseInt(JSON.parse(e.data).cmd.ResultCode);
                        // console.log(this.boardinfo);
                        switch (staut) {
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
                              message: "Recording failed!",
                              duration: 0,
                              showClose: true,
                            }); //录制失败
                            break;
                          case 2:
                            that.$message({
                              message:
                                "The card channel ID has been used and can be modified or re added in the edit box!", //板卡板卡通道ID已经被使用，可在编辑框修改或者重新添加
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
                    outboolen == false;
                  }
                  //外置存储外置存储外置
                }
              }, 800);
            }
          }
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
            message: "Please do not click frequently！", //请不要频繁点击
            type: "warning",
            duration: 0,
            showClose: true,
          });
        }
        //提示不要频繁点击

        this.recordboolen = false;

        //操作数据清空
        setTimeout(() => {
          this.RecordData = [];
          this.removeData = [];
        }, 1000);
      } else {
        return;
      }
    },
  },
};
</script>


<style scoped >
#father {
  position: relative;
}
#child {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
  border: none;
  opacity: 0;
}

#a {
  display: none;
}
.record {
  list-style-type: none !important ;
}
#table {
  width: 98%;
  height: auto;
  margin: 0rem auto;
  max-height: 800px;
  overflow-x: scroll;
}
.table {
  width: 100%;
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
.table td input {
  outline-color: aliceblue;
  border: none;
  color: gray;
  text-align: center;
  /* width: 100%;
  height: 100%; */
}
.table td .text {
  outline-color: rgb(37, 141, 222);
  border: 1px solid rgb(231, 236, 247);
  /* color: gray; */
  text-align: center;
  width: 95%;
  height: 50%;
  border-radius: 5px;
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
  width: 190px;
}

/* 这是控制描述信息那列数据 */
.table tr {
  width: 15%;
}
.table tr td:nth-child(2) {
  /* background-color: palegreen; */
  width: 15%;
}
.record >>> .des {
  color: rgb(37, 141, 222);
  /* background-color: pink; */
  display: inline-block;
  width: 60%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  cursor: pointer;
}
/* 这是控制描述信息那列数据 */

.el-input {
  margin-left: -5%;
  width: 50%;
}
.record >>> .el-input--suffix .el-input__inner {
  margin-left: 35% !important;
}
.record >>> .el-dialog {
  width: 40%;
  box-shadow: 0 1px 3px rgb(0 0 0 / 5%);
}
.record >>> .el-select-dropdown {
  margin-left: 7%;
}
.record >>> .ui-widget-content {
  border: none;
  background: none;
}
.record >>> .el-select-dropdown {
  margin-left: 76px !important;
}
#Showore {
  font-size: 1.7rem;
}
.record >>> .el-input__suffix {
  margin-right: -41px;
}
.record[data-v-d8b20e8a] .ui-widget-content {
  height: 85%;
}
.record >>> .select {
  /* background-color: pink; */
  margin-left: 5px;
  width: 35%;
}
.record >>> #edit {
  /* background-color: pink; */
  width: 8rem;
  margin-left: 50px;
}
@media screen and (min-width: 1353px) and (max-width: 1683px) {
  .record >>> .el-select > .el-input {
    margin-left: -19% !important;
  }
  .record >>> .el-dialog {
    width: 40%;
  }
  .table {
    width: 95%;
    height: auto;
    /* background-color: pink; */
    margin: 0rem auto;
  }
  .hearder > .left[data-v-fae5bece] {
    width: -11%;
  }
}
@media screen and (min-width: 1000px) and (max-width: 1330px) {
  .record >>> .el-dialog {
    width: 60%;
  }
  .table {
    width: 100%;
    height: auto;
    /* background-color: pink; */
    margin: 0rem auto;
  }
}
@media screen and (min-width: 300px) and (max-width: 1000px) {
  .record >>> .el-select > .el-input {
    margin-left: -19% !important;
  }
  .record >>> .el-dialog {
    width: 80%;
  }
  .record >>> .el-input--suffix .el-input__inner {
    margin-left: 19% !important;
    padding-right: 0px;
  }
  .record >>> .el-input__inner {
    width: 110%;
  }
  .table {
    width: 150%;
    height: auto;
    /* background-color: pink; */
    margin: 0rem auto;
  }
}
@media screen and (min-width: 100px) and (max-width: 600px) {
  .table {
    width: 200%;
    height: auto;
    /* background-color: pink; */
    margin: 0rem auto;
  }
}
</style>