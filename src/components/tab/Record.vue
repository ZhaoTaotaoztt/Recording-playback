<!--
 * @Author: your name
 * @Date: 2022-02-08 11:16:03
 * @LastEditTime: 2022-02-21 16:25:02
 * @LastEditors: Please set LastEditors
 * @Description: 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
 * @FilePath: \admin\src\components\tab\Record.vue
-->
<template>
  <div class="record">
    <div id="table">
      <table>
        <tr>
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
// import $ from 'jquery'
// import 'jquery-ui-dist/jquery-ui'
// import 'jquery-ui-dist/jquery-ui.min.css'

export default {
  data() {
    return {
      record: {},
      recordid: [],
      recordIndex: [],
      recordItem: [],
      recordboolen: false,
      
      recordboolen0: true,
      recordboolen1: true,

      isclick: true,

      dialogVisible: false,
      dialogrecord: false,

      recordform: {
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
    this.getRecord();
  },
  methods: {
    //record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分record部分
    getRecord() {
      //获取record数据
      this.axios.get("/record").then((res) => {
        this.record = res.data;
        // this.Deleterecord();
        // console.log(this.record);
      });
    },
    addRecordData() {
      //添加新的数据到record
      this.dialogrecord = false;
      this.axios.post("/record", this.recordform).then((res) => {
        // console.log(res);
        // console. log(1);
        this.getRecord();
      });
    },
    Checkedrecord(item) {
      if (item.checked == true) {
        this.recordboolen = true;
        this.$nextTick(() => {
          if (this["recordboolen" + item.ChanneIndex]) {
            console.log(this["recordboolen" + item.ChanneIndex]);
            this["recordboolen" + item.ChanneIndex] = false;
            item.checked = true;
            // console.log(item.id);
            this.recordid.push(item.id);
            this.recordItem.push(item);
            // console.log(item);
          } else {
            item.checked = false;
            console.log(this["recordboolen" + item.ChanneIndex]);
            this["recordboolen" + item.ChanneIndex] = this.record.every(
              val => {
                if (val.ChanneIndex == item.ChanneIndex) {
                  item.checked = false;
                  return val.checked == false;
                } else {
                  return true;
                }
              }
            );
          }
          // return true
        });
      } else {
        this.recordboolen = false;
      }
    },
    Deletrecord() {
      //删除复选框选中的信息
      if (this.recordboolen == true) {
        this.$confirm("确定要删除这条信息吗？")
          .then((_) => {
            for (let i = 0; i < this.recordid.length; i++) {
              this.axios.delete("/record/" + this.recordid[i]).then((res) => {
                this.getRecord();
                this.recordid = [];
                this.recordboolen0 = false;
                this.recordboolen1 = false;
                this.record.checked = false;
                this.recordboolen = false;
              });
            }
          })
          .catch((err) => {
            this.recordboolen0 = false;
            this.recordboolen1 = false;
            this.record.checked = false;
            console.log(err);
          });
      } else {
        return;
      }
    },
    StartRecord() {
      console.log(this.recordItem);
      console.log("单独下发请求");
      // console.log(this.item);
      // if (this.recordIndex.length >= 2) {
      //   console.log('klfji');
      //   console.log(this.recordIndex.indexOf('0'));
      //   // for (var i = 0; i < this.recordIndex.length; i++) {
      //     // console.log(this.recordIndex[i]);
      //     // if (
      //     //   this.recordIndex.indexOf('0')
      //     //   // &&
      //     //   // this.recordIndex.indexOf(this.recordIndex[i] == 1)
      //     // ) {
      //     //   console.log('成功');
      //     //   this.record.checked=false
      //     // }else{
      //     //      this.$message({
      //     //     type: "warning",
      //     //     message: "此操作不能同时选择ChannelIndex值相同的行",
      //     //   });
      //     // }
      //   // }
      // } else {
      //   console.log('发送单独的请求');
      //   //这里下发请求单条数据进行下发
      // }

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
    StartSynRecord(){
      console.log('组合下发');

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
    }
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
  border: rgb(245, 154, 35) 2px solid;
}
.record >>> table .el-checkbox__inner {
  width: 16px;
  height: 16px;
  border: rgb(245, 154, 35) 2px solid;
}
.record >>> p .el-button {
  background-color: rgb(245, 154, 35);
  color: white;
  font-size: 1rem;
  margin-left: 50px;
  width: 250px;
}

</style>