<template>
    <el-select :value.sync="selectedAppid" size="small" v-loading="dataListLoading"  @change="selectAccount" filterable>
        <el-option v-for="item in accountList" :key="item.appid" :label="item.name+'（'+ACCOUNT_TYPES[item.type]+'）'" :value="item.appid"></el-option>
    </el-select>
</template>
<script>
import { mapState } from 'vuex'
export default {
    data() {
        return {
            dataListLoading: false
        }
    },
    computed: mapState({
        accountList: state=>state.wxAccount.accountList,
        ACCOUNT_TYPES: state=>state.wxAccount.ACCOUNT_TYPES,
        selectedAppid:state=>state.wxAccount.selectedAppid
    }),
    mounted(){
        this.getDataList()
    },
    methods:{
        getDataList() {
            this.dataListLoading = true
            this.$http({
                url: this.$http.adornUrl('/manage/wxAccount/list'),
                method: 'post',
                data: this.$http.adornParams({
                    'sysUserId': localStorage.getItem('userId')
                })
            }).then(({ data }) => {
                if (data && data.code === 200) {
                    this.$store.commit('wxAccount/updateAccountList', data.list)
                    if(!data.list.length){
                        this.$message.warning("频道列表为空，请先到频道管理 - 频道账号页面添加频道")
                    }
                }
                this.dataListLoading = false
            })
        },
        selectAccount(appid){
            localStorage.setItem('appid', appid)
            if(this.selectedAppid!=appid){
                this.$store.commit('wxAccount/selectAccount', appid)
            }
        }
    }
}
</script>