<template>
    <div>
        <div class="search-bar">
            <div class="block">
                <el-row :gutter="10">
                    <el-col :xs="24" :sm="24" :md="4">
                        <el-input v-model="user.phone_like" size="small" placeholder="请输入手机号"></el-input>
                    </el-col>
                    <el-col :xs="24" :sm="24" :md="4">
                        <el-input v-model="user.name_like" size="small" placeholder="请输入用户姓名"></el-input>
                    </el-col>
                    <el-col :xs="24" :sm="24" :md="10">
                        <el-date-picker
                                v-model="currentTimeData"
                                size="small"
                                type="datetimerange"
                                :picker-options="timeData"
                                range-separator="至"
                                start-placeholder="开始日期"
                                end-placeholder="结束日期"
                                @change="handleChange"
                                value-format="yyyy-MM-dd HH:mm:ss"
                                align="right">
                        </el-date-picker>
                    </el-col>
                    <el-col :xs="24" :sm="24" :md="6" class="tr">
                        <el-button class="search" type="primary" size="small" icon="el-icon-search" @click="search()">搜索
                        </el-button>
                        <el-button class="download" size="small" icon="el-icon-download" @click="downLoad()">下载
                        </el-button>
                    </el-col>
                </el-row>
            </div>
        </div>
        <el-table
                v-loading="loading"
                :data="data"
                style="width: 100%"
                border
                size="small"
                highlight-current-row
                :height="componentHeight"
                max-height="600px">
            <el-table-column
                    fixed
                    prop="userName"
                    label="姓名"
                    width="200">
            </el-table-column>
            <el-table-column
                    prop="gmtCreated"
                    label="填写时间">
            </el-table-column>
            <el-table-column
                    prop="phone"
                    label="联系方式"
                    width="400">
            </el-table-column>
            <el-table-column
                    prop="wechat"
                    label="微信号"
                    width="200">
            </el-table-column>
        </el-table>
        <div class="pagination-block">
            <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="pageInfo.currentPage"
                    :page-sizes="pageInfo.pageSizes"
                    :page-size="pageInfo.pageSize"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="pageInfo.total">
            </el-pagination>
        </div>
    </div>
</template>

<script type="text/babel">
    import ElInput from "../../node_modules/element-ui/packages/input/src/input.vue";
    import ajax from '../src/util/http'
    import ElButton from "../../node_modules/element-ui/packages/button/src/button.vue";
    import ElRow from "element-ui/packages/row/src/row";

    export default {
        components: {
            ElRow,
            ElButton,
            ElInput
        },
        props: ['height'],
        data() {
            return {
                user: {
                    phone_like: '',
                    name_like: '',
                    start_time: '',
                    end_time: '',
                },
                pageInfo: {
                    total: 20,
                    pageSize: 20,
                    pageSizes: [1, 20, 100, 200, 300, 400],
                    currentPage: 1,
                },
                componentHeight: '',
                currentTimeData: '',
                timeData: {
                    shortcuts: [{
                        text: '最近一天',
                        onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24);
                            picker.$emit('pick', [start, end]);
                        }
                    }, {
                        text: '最近一周',
                        onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', [start, end]);
                        }
                    }]
                },
                data: [],
                loading: true
            }
        },
        created: function () {
            this.handleAjax();
        },
        updated: function () {
            this.componentHeight = this.height - 125;
        },
        methods: {
            search: function () {
                this.handleAjax();
            },
            downLoad: function () {
                this.$message.warning('当前功能暂未开放~');
            },
            handleSizeChange(val) {
                // console.log(`每页 ${val} 条`);
                this.pageInfo.pageSize = val;
                this.handleAjax();
            },
            handleCurrentChange(val) {
                // console.log(`当前页: ${val}`);
                this.pageInfo.currentPage = val;
                this.handleAjax();
            },
            handleChange: function (value) {
                console.log(value);
                this.currentTimeData = value;
                this.user.start_time = value[0];
                this.user.end_time = value[1];
            },
            handleAjax: function () {
                var that = this;
                that.loading = true;
                ajax.get({
                    url: 'query_user',
                    data: {
                        page_size: that.pageInfo.pageSize,
                        page: that.pageInfo.currentPage,
                        phone_like: that.user.phone_like,
                        name_like: that.user.name_like,
                        start_time: that.user.start_time,
                        end_time: that.user.end_time
                    }
                }, function (res) {
                    console.log(res);
                    that.data = res.data.data;
                    that.pageInfo.total = res.data.totalCount;
                    that.loading = false;
                }, function (e) {
                    console.log(e);
                    that.$message.error(e.data.msg);
                    that.loading = false;
                });
            }
        },
    }
</script>

<style lang="scss" type="text/scss" scoped="">
    @import "../style/common/global";
    .el-col{
        margin-bottom: 10px;
    }
    .search-bar {
        .search, .download {
            position: relative;
            top: -1px;
        }
    }

    .block {
        margin: 15px 0;
    }

    .pagination-block {
        text-align: right;
        margin-top: 15px;
    }
</style>