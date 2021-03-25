<template>

    <div>
        <div>
          <el-row>
            <el-col :span="3"><div>
              <el-input v-model="inputRevId" placeholder="请输入检查单 如： 100001"></el-input>
            </div></el-col>
            <el-col :span="6"><div>
              <el-input v-model="inputRevDataLimit" placeholder="请输入限定日期 如： 2021-4-1 ~ 2021-4-1"></el-input>
            </div></el-col>
            <el-col :span="6"><div>
            <el-select v-model="selectValues">
              <el-option v-model="selectRole">
                role 1
              </el-option>
              <el-option v-model="selectRole">
                role 2
              </el-option>
              <el-option>
                role 3
              </el-option>
            </el-select>
            </div></el-col>
            <el-col :span="6"><div>
              <el-button  @click="beginOpt">写数据</el-button>
            </div></el-col>
            <el-col :span="6"><div>
              <el-button>开始预约</el-button>
            </div></el-col>
          </el-row>
        </div>

        <div>
          <h2>获得检查项目：</h2>
          <el-row class="grid-content">
<!--          暂定不写-->
            <h2 style="color: red">CT1</h2>
            <h2 style="color: red">CT2</h2>
            <h2 style="color: red">UB1</h2>
          </el-row>
        </div>

        <div>
          <el-row>
            <el-col :span="3"><div><el-input placeholder="选中的方案 填写序号 如：1"></el-input></div></el-col>
            <el-col :span="3"><div><el-button>选中</el-button></div></el-col>
          </el-row>


          <el-table :data="tableDataRev">
            <el-table-column prop="patientName" label="姓名">

            </el-table-column>

            <el-table-column prop="patientRevId" label="检查单号">

            </el-table-column>

            <el-table-column prop="patientRevDate" label="预约日期">

            </el-table-column>

            <el-table-column prop="patientRevTime" label="预约时间">

            </el-table-column>
          </el-table>
        </div>
<!--        <el-table-->
<!--                :data="tableData"-->
<!--                border-->
<!--                style="width: 100%"-->
<!--                class="table">-->
<!--            <el-table-column-->
<!--                    fixed-->
<!--                    prop="id"-->
<!--                    label="item_id"-->
<!--                    width="100">-->
<!--            </el-table-column>-->
<!--            <el-table-column-->
<!--                    prop="username"-->
<!--                    label="username"-->
<!--                    width="120">-->
<!--            </el-table-column>-->
<!--            <el-table-column-->
<!--                    prop="email"-->
<!--                    label="email"-->
<!--                    width="120">-->
<!--            </el-table-column>-->
<!--            <el-table-column-->
<!--                    prop="phone"-->
<!--                    label="phone"-->
<!--                    width="130">-->
<!--            </el-table-column>-->
<!--            <el-table-column-->
<!--                    prop="sex"-->
<!--                    label="sex"-->
<!--                    width="100">-->
<!--            </el-table-column>-->
<!--            <el-table-column-->
<!--                    prop="zone"-->
<!--                    label="zone"-->
<!--                    width="100">-->
<!--            </el-table-column>-->
<!--            <el-table-column-->
<!--                    prop="create_datetime"-->
<!--                    label="create_datetime"-->
<!--                    width="300"-->
<!--                    :formatter="formatter">-->
<!--            </el-table-column>-->
<!--            <el-table-column-->
<!--                    fixed="right"-->
<!--                    label="Operation"-->
<!--                    width="100">-->
<!--                <template scope="scope">-->
<!--                    <el-button @click="editItem(scope.$index, tableData)" type="text" size="large">Edit</el-button>-->
<!--                </template>-->
<!--            </el-table-column>-->
<!--        </el-table>-->
<!--        <el-pagination class="pagination" layout="prev, pager, next" :total="total" :page-size="pageSize"-->
<!--                       v-on:current-change="changePage">-->
<!--        </el-pagination>-->
<!--        <db-modal :dialogFormVisible="dialogFormVisible" :form="form" v-on:canclemodal="dialogVisible"></db-modal>-->
    </div>

</template>

<script>
    import Bus from '../eventBus'
    import DbModal from './DbModal.vue'

    export default {
        data(){
            return {
                inputRevId:'',
                inputRevDataLimit:'',

                tableData: [],
                apiUrl: 'http://127.0.0.1:8000/api/persons',
                optApiUrl:'http://127.0.0.1:80/medicalExam/hello',
                selectValues:'',
                selectRole:'',
                total: 0,
                pageSize: 10,
                currentPage: 1,
                sex: '',
                email: '',
                dialogFormVisible: false,
                form: '',

                tableDataRev:[{
                  patientName:'张三',
                  patientRevId:'10001',
                  patientRevDate:'2021-4-1',
                  patientRevTime:'13:00'
                },{
                  patientName:'李四',
                  patientRevId:'10001',
                  patientRevDate:'2021-4-1',
                  patientRevTime:'9:00'
                },{
                  patientName:'赵武',
                  patientRevId:'10001',
                  patientRevDate:'2021-4-1',
                  patientRevTime:'17:00'
                },{
                  patientName:'王柳',
                  patientRevId:'10001',
                  patientRevDate:'2021-4-1',
                  patientRevTime:'15:00'
                }]


            }
        },
        components: {
            DbModal
        },
        mounted () {
            this.getCustomers();
            Bus.$on('filterResultData', (data) => {
                this.tableData = data.results;
                this.total = data.total_pages;
                this.pageSize = data.count;
                this.email = data.email;
                this.sex = data.sex;

            });
        },

        methods: {

          // function by simzhang
            beginOpt: function () {
              console.log("simzhang bestin opt")
              this.$axios.get(this.optApiUrl)
                .then((response) => {
                  // print log
                  console.log(response.data)
                  console.log(this.inputRevId)
                  console.log(this.inputRevDataLimit)
                  console.log(this.selectValues)
                  console.log(this.selectRole)
                })
            },

            dialogVisible: function () {
                this.dialogFormVisible = false;
            },

            getCustomers: function () {
                this.$axios.get(this.apiUrl, {
                    params: {
                        page: this.currentPage,
                        sex: this.sex,
                        email: this.email
                    }
                }).then((response) => {
                    this.tableData = response.data.data.results;
                    this.total = response.data.data.total;
                    this.pageSize = response.data.data.count;
                    console.log(response.data.data);
                }).catch(function (response) {
                    console.log(response)
                });
            },
            changePage: function (currentPage) {
                this.currentPage = currentPage;
                this.getCustomers()
            },
            editItem: function (index, rows) {
                this.dialogFormVisible = true;
                const itemId = rows[index].id;
                const idurl = 'http://127.0.0.1:8000/api/persons/detail/' + itemId;
                this.$axios.get(idurl).then((response) => {
                    this.form = response.data;
                }).catch(function (response) {
                    console.log(response)
                });
            },

            formatter(row, column) {
                let data = this.$moment(row.create_datetime, this.$moment.ISO_8601);
                return data.format('YYYY-MM-DD')
            },
        }
    }
</script>

<style>
    .table {
        margin-top: 30px;
    }

    .pagination {
        margin-top: 10px;
        float: right;
    }


    .el-row {
      margin-bottom: 20px;
    &:last-child {
       margin-bottom: 0;
     }
    }
    .el-col {
      border-radius: 4px;
    }
    .bg-purple-dark {
      background: #99a9bf;
    }
    .bg-purple {
      background: #d3dce6;
    }
    .bg-purple-light {
      background: #e5e9f2;
    }
    .grid-content {
      border-radius: 4px;
      min-height: 36px;
    }
    .row-bg {
      padding: 10px 0;
      background-color: #f9fafc;
    }


    .el-row {
      margin-bottom: 20px;
    &:last-child {
       margin-bottom: 0;
     }
    }
    .el-col {
      border-radius: 4px;
    }
    .bg-purple-dark {
      background: #99a9bf;
    }
    .bg-purple {
      background: #d3dce6;
    }
    .bg-purple-light {
      background: #e5e9f2;
    }
    .grid-content {
      border-radius: 4px;
      min-height: 200px;
    }
    .row-bg {
      padding: 10px 0;
      background-color: #f9fafc;
    }



</style>
