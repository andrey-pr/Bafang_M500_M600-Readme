<template>
    <div class="handle-up-ticket-list">
        <div class="header">
            <el-form :inline="true" class="demo-form-inline">
                <el-form-item :label="$t('category')">
                    <el-select v-model="search.category" placeholder="" @change="getHandleUpTicketList">
                        <el-option
                            v-for="item in categoryList"
                            :key="item.id"
                            :label="item.value"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item :label="$t('status')">
                    <el-select v-model="search.status" placeholder="" @change="getHandleUpTicketList">
                        <el-option
                            v-for="item in statusList"
                            :key="item.id"
                            :label="item.value"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
        </div>
        <div class="table-wrap">
            <table class="table-content" border="0" cellspacing="0" cellpadding="0">
                <thead>
                <td v-for="(item,index) in tableHead" :class="{'al-right': index === 0}">{{ item }}</td>
                </thead>
                <tbody>
                <tr v-for="item in ticketList">
                    <td class="al-right">{{item.sn}}</td>
                    <td>{{item.title}}</td>
                    <td>{{item.target_sn}}</td>
                    <td class="center-name">{{item.org.name}}</td>
                    <td>{{item.create_time | timeFormat("Y-m-d")}}</td>
                    <td>{{item.status | ticketStatus}}</td>
                    <td>
                        <el-button class="btn-underline" type="text" @click="goToTicketDetailPage(item)">Look</el-button>
                    </td>
                </tr>
                </tbody>
            </table>
            <div class="no-data-content" v-show="ticketList==false">
                {{ $t('noResult') }}
            </div>
            <div class="table-footer">
                <el-pagination
                    @current-change="handleCurrentChange"
                    :current-page.sync="currentPage"
                    :page-size="10"
                    layout="prev, pager, next"
                    :total="total">
                </el-pagination>
                <p class="total">{{ $t('total') }} {{total}}</p>
            </div>
        </div>
    </div>
</template>

<script>
    import Core from '../../../core/core';
    import ZH from 'src/assets/lang/zh';
    import EN from 'src/assets/lang/en';
    import DE from 'src/assets/lang/de';
    import NL from 'src/assets/lang/nl';

    export default {
        name: "myTicketList",

        data() {
            return {
                tableHead: ['Service No.', 'Title', 'Target SN', 'Service Center', 'Create Time', 'Status', 'Operation'],
                currentPage: 1,
                ticketList: [],
                total: 0,

                categoryList: [
                    {id: 0, value: 'All'},
                    {id: 1, value: 'Vehicle'},
                    {id: 2, value: 'Motor'},
                    {id: 3, value: 'HMI'},
                    {id: 4, value: 'Sensor'},
                    {id: 5, value: 'Battery'},
                    {id: 6, value: 'Controller'},
                    {id: 7, value: 'Light'},
                    {id: 8, value: 'Other'}
                ],
                statusList: [
                    {id: -1, value: 'All'},
                    {id: 100, value: 'Pending Assign'},
                    {id: 200, value: 'Processing'},
                    {id: 300, value: 'Complete'},
                ],
                search: {
                    category: 0,
                    status: -1,
                }
            }
        },
        i18n: {
            messages: {
                en: EN.ServiceCenter.handleUpTicketList,
                zh: ZH.ServiceCenter.handleUpTicketList,
                de: DE.ServiceCenter.handleUpTicketList,
                nl: NL.ServiceCenter.handleUpTicketList
            }
        },
        mounted: function () {
            // 国际化
            this.initData();
            this.$root.eventHub.$on(Core.Const.DATA.KEY_I18N_UPDATE, () => {
                setTimeout(() => {
                    this.initData();
                }, 100);
            });

            this.getHandleUpTicketList();
        },
        methods: {
            initData() {
                this.tableHead = [this.$t('tableHead.serviceNo'), this.$t('tableHead.title'), this.$t('tableHead.targetSn'), this.$t('tableHead.createTime'), this.$t('tableHead.status'), this.$t('tableHead.operation')];
                this.categoryList = [
                    {id: 0, value: this.$t('categoryList.all')},
                    {id: 1, value: this.$t('categoryList.vehicle')},
                    {id: 2, value: this.$t('categoryList.motor')},
                    {id: 3, value: this.$t('categoryList.hmi')},
                    {id: 4, value: this.$t('categoryList.sensor')},
                    {id: 5, value: this.$t('categoryList.battery')},
                    {id: 6, value: this.$t('categoryList.controller')},
                    {id: 7, value: this.$t('categoryList.light')},
                    {id: 8, value: this.$t('categoryList.other')}
                ];
                this.statusList = [
                    {id: -1, value: this.$t('statusList.all')},
                    {id: 100, value: this.$t('statusList.pendingAssign')},
                    {id: 200, value: this.$t('statusList.processing')},
                    {id: 300, value: this.$t('statusList.complete')},
                ];
            },

            handleCurrentChange(val) {
                this.currentPage = val;
                this.getHandleUpTicketList();
            },

            getHandleUpTicketList() {
                Core.Api.Ticket.getTicketOfHandleUp(this.search.category, this.search.status, this.currentPage).then(res => {
                    this.ticketList = res.list;
                    this.total = res.count;
                });
            },

            goToTicketDetailPage(ticket) {
                this.$router.push({path: "detail", query: {id: ticket.id}})
            }
        }
    }
</script>

<style lang="scss" rel="stylesheet/scss">
    .handle-up-ticket-list {
        height: 100%;
        .dz-started {
            display: inline-block;
            width: 120px;
            height: 120px;
            .dz-message {
                width: 100%;
                height: 100%;
                img {
                    width: 120px;
                    height: 120px;
                }
            }
        }

        .img-list {
            float: left;
            img {
                width: 120px;
                height: 120px;
                margin-right: 10px;
            }
        }
        .el-upload-list__item img {
            display: none;
        }

        .el-form-item__label {
            color: #A1A5B1;
        }
        .center-name{
            max-width: 320px;
            text-overflow: ellipsis;
            overflow: hidden;
        }
    }
</style>



// WEBPACK FOOTER //
// handle-up-ticket-list.vue?83301cba