<template>
  <section class="table_container">
    <el-col
      :span="24"
      class="toolbar toolbar_title"
      style="padding-bottom: 0px; height: 100px;padding-top: 30px"
    >
      <el-form :inline="true" :model="filters" class="toolbar_form">
        <el-form-item class="f-left search_input">
          <el-input v-model="filters.name" placeholder="患者姓名">
            <template slot="append" icon="el-icon-search">
              <el-button
                type="primary"
                v-on:click="getEwList"
                style="background-color: #52d7ac; border-radius: 0; color: #fff; border: 1px solid #52d7ac"
              >
                <i class="el-icon-search" style="margin-right: 5px"></i>搜索
              </el-button>
            </template>
          </el-input>
        </el-form-item>
      </el-form>
      <el-select v-model="filters.status" placeholder="请选择状态" @change="getEwList">
        <el-option :value="1" label="未完成"></el-option>
        <el-option :value="0" label="已完成"></el-option>
      </el-select>
    </el-col>
    <!--列表-->
    <el-table
      :data="ewArray"
      :border="true"
      stripe
      highlight-current-row
      v-loading="listLoading"
      style="width: 100%;"
    >
      <el-table-column prop="patientName" align="center" label="患者姓名" sortable></el-table-column>
      <el-table-column prop="content" align="center" label="提醒信息" sortable></el-table-column>
      <el-table-column prop="status" align="center" label="状态" :formatter="formatSatus" sortable></el-table-column>
      <el-table-column align="center" label="操作">
        <template slot-scope="scope">
          <el-button
            type="success"
            style="background-color: #52d7ac; border-radius: 0; color: #fff; border: 1px solid #52d7ac;padding: 10px 30px"
            @click="changelInfo(scope.$index, scope.row)"
            :disabled="scope.row.status==0?true:false"
          >{{scope.row.status==0?'已完成':'完成'}}</el-button>
          <el-button
            type="success"
            style="background-color: #52d7ac; border-radius: 0; color: #fff; border: 1px solid #52d7ac;padding: 10px 30px"
            @click="openIM(scope.$index, scope.row)"
          >联系患者</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!--工具条-->
    <!-- <el-pagination
      @size-change="handlePageSizeChange"
      @current-change="handlePageCurrentChange"
      :current-page="page.current"
      :page-sizes="page.sizes"
      :page-size="page.size"
      :layout="page.layout"
      :total="page.total"
    ></el-pagination> -->
  </section>
</template>

<script>
import { pagination } from "@/mixins";
import menus from '../components/menus'
import {mapActions} from 'vuex'
export default {
  mixins: [pagination],
  data() {
    return {
      status: "",
      filters: {
        name: "",
        status: ""
      },
      value: "",
      listLoading: false,
      usersList: [],
      user: null,
      ewArray: [],
      timer: '',
      status: false,
      datalength:''
    };
  },
  created() {
    this.getEwList()
  },
  computed: {
    s() {
      return this.$store.state.status.status
    }
  },
  methods: {
    // 打开聊天窗口
    openIM(index, row) {
      //location.href="../../static/IM/im/main.html";
      sessionStorage.setItem("openIMPersonInfo", JSON.stringify(row));
      //获取计划列表
      this.$http({
        url: "/api/plan/getPlanByPatientId?patientId=" + row.id
      })
        .then(res => {
          sessionStorage.setItem(
            "openIMPlanList",
            JSON.stringify(res.data.list)
          );
          var openIMVisitList = {};
          res.data.list.forEach(item => {
            // 获取随访列表
            this.$http({
              url:
                "/api" +
                "/visitRecord/getVisistManagerList?planId=" +
                item.planId
            })
              .then(res => {
                sessionStorage.setItem(item.planId, JSON.stringify(res.data));
              })
              .catch(err => {
                console.log(err);
              });
          });
        })
        .catch(err => {
          console.log(err);
        });
      let account = row.yunXinAccount;
      if (account) {
        window.open("../../static/IM/im/main.html?account=" + account);
      } else {
        //todo  删除
        account = "test99";
        window.open("../../static/IM/im/main.html?account=" + account);
        this.$message.warning("患者云信账号信息为空，无法打开聊天界面！");
      }
    },
    ...mapActions([
      'setStatus'
    ]),
    handleSearch() {
      this.getEwList();
    },
    // 分页
    handlePageCurrentChange(val) {
      this.page.current = val;
      this.getEwList();
    },
    // 获取体征预警列表
    getEwList() {
      this.$http
        .get(
          "/api" +
            `/notice/getDoctorNoticeList?userId=${this.$store.state.user.user.id}&titleType=1&receiverRole=${this.$store.state.user.user.type}&noticeType=1&name=${this.filters.name}&status=${this.filters.status}`
        )
        .then(res => {
          this.ewArray = res.data.list;

          this.page.total = res.data.total;
          this.editStorage(res.data.total)
        })
        .catch(err => {
          console.log(err);
        });
    },
    formatSatus(row) {
      return row.status == 0 ? "已完成" : row.status == 1 ? "未完成" : "无";
    },
    editStorage(earlyWarningCount) {

      let loginUser =  JSON.parse(sessionStorage.getItem('loginUser'))

      let menu = loginUser.menu

      let arr = menu.map((v, i) => {
        if(v.id === 4) {
          v.submenu[1].earlyWarningCount = earlyWarningCount
          return v
        } else {
          return v
        }
      })

      loginUser.menu = arr

      console.log(loginUser.menu)

      sessionStorage.setItem('loginUser', JSON.stringify(loginUser))

      this.setStatus(this.s + 1)
    },

    changelInfo(index, row) {
      this.$http.post('/api/user/completeOperation',{id:row.id}).then(res=>{
        this.editStorage(res.data)
      }).catch(err=>{
        console.log(err)
      })
      this.$http
        .post("/api" + `/notice/updateStatus`, { messageId: row.id })
        .then(res => {
          if (res.data) {
            this.$message.success("操作成功")
            this.getEwList()
          } else {
            this.$message.error("操作失败")
          }
        })
        .catch(err => {
          console.log(err);
        });
      console.log(row)
    },
  }
}
</script>

<style scoped>
.toolbar_page {
  margin-top: 20px;
  text-align: center;
}

.toolbar_title {
  background-color: #fff;
  margin-bottom: 20px;
  box-sizing: border-box;
  padding: 0;
}

.toolbar_form {
  padding: 0 20px;
}

.toolbar_title h3 {
  height: 60px;
  line-height: 60px;
  background-color: #edfbf7;
  padding: 0 20px;
  margin: 0;
  font-size: 20px;
  margin-bottom: 30px;
}

.table_container >>> .el-table__row--striped td {
  background-color: #edfbf7 !important;
}

.table_container >>> .el-table .el-table__header thead th {
  background-color: #edfbf7 !important;
}

.el-dialog__wrapper {
  position: absolute !important;
}

.toolbar >>> .el-input__inner {
  border: 1px solid #52d7ac;
}

.search_input >>> .el-form-item__content {
  width: 500px;
}
</style>
