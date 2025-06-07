<template>
    <view class="content">
        <form class="app-update-pv">
            <view
                    :style='{"boxShadow":"0","backgroundColor":"rgba(255, 255, 255, 0.25)","borderColor":"rgba(228, 232, 230, 1)","margin":"0 0 0px 0","borderWidth":"2rpx","borderStyle":"none none solid none ","height":"100rpx"}'
                    class="cu-form-group">
                <view :style='{"width":"160rpx","fontSize":"28rpx","color":"var(--publicMainColor)","textAlign":"left"}'
                      class="title">预约时间</view>
                <input
                        :style='{"padding":"0 30rpx","boxShadow":"0 0 0px rgba(0,0,0,.6) inset","margin":"0","borderColor":"var(--publicMainColor)","backgroundColor":"#fff","color":"rgba(0, 0, 0, 1)","textAlign":"left","borderRadius":"0","borderWidth":"10rpx 2rpx","fontSize":"28rpx","borderStyle":"solid","height":"88rpx","marginTop":"7rpx"}'
                        v-model="ruleForm.jiaolianYuyueTime" placeholder="预约时间" :readonly="ro.jiaolianYuyueTime"
                        @tap="toggleTab('jiaolianYuyueTime')"></input>
            </view>
					<!-- text后缀 -->
            <view
                    :style='{"boxShadow":"0","backgroundColor":"rgba(255, 255, 255, 0.25)","borderColor":"rgba(228, 232, 230, 1)","margin":"0 0 0px 0","borderWidth":"2rpx","borderStyle":"none none solid none ","height":"210rpx"}'
                    class="cu-form-group">
                <view :style='{"width":"160rpx","fontSize":"28rpx","color":"var(--publicMainColor)","textAlign":"left"}'
                      class="title">预约备注</view>
                <textarea :style='{"padding":"0 30rpx","boxShadow":"0 0 0px rgba(0,0,0,.6) inset","margin":"0","borderColor":"var(--publicMainColor)","backgroundColor":"#fff","color":"rgba(0, 0, 0, 1)","textAlign":"left","borderRadius":"0","borderWidth":"10rpx 2rpx","fontSize":"28rpx","borderStyle":"solid","height":"88rpx","marginTop":"7rpx","height":"200rpx"}'
                          :disabled="ro.jiaolianYuyueText" v-model="ruleForm.jiaolianYuyueText" placeholder="预约备注"/>
            </view>

            <view class="btn">
                <button
                        :style='{"boxShadow":"0 0 16rpx rgba(0,0,0,0) inset","backgroundColor":"var(--publicMainColor)","borderColor":"#409EFF","borderRadius":"8rpx","color":"rgba(255, 255, 255, 1)","borderWidth":"0","width":"70%","fontSize":"32rpx","borderStyle":"solid","height":"80rpx"}'
                        @tap="onSubmitTap" class="bg-red">提交</button>
            </view>
        </form>

					<w-picker mode="dateTime" step="1" :current="false" :hasSecond="false" @confirm="jiaolianYuyueTimeConfirm"
                              ref="jiaolianYuyueTime" themeColor="#333333"></w-picker>
					<w-picker mode="dateTime" step="1" :current="false" :hasSecond="false" @confirm="insertTimeConfirm"
                              ref="insertTime" themeColor="#333333"></w-picker>
					<w-picker mode="dateTime" step="1" :current="false" :hasSecond="false" @confirm="createTimeConfirm"
                              ref="createTime" themeColor="#333333"></w-picker>


    </view>
</template>

<script>
    import wPicker from "@/components/w-picker/w-picker.vue";

    export default {
        data() {
            return {
                cross: '',
                ro:{
					jiaolianId: false,
					yonghuId: false,
					jiaolianYuyueTime: false,
					jiaolianYuyueText: false,
					insertTime: false,
					jiaolianYuyueYesnoTypes: false,
					jiaolianYuyueYesnoText: false,
					createTime: false,
        },
            ruleForm: {
					jiaolianId: '',
					yonghuId: '',
					jiaolianYuyueTime: '',
					jiaolianYuyueText: '',
					insertTime: '',
					jiaolianYuyueYesnoTypes: '',//数字
					jiaolianYuyueYesnoValue: '',//数字对应的值
					jiaolianYuyueYesnoText: '',
					createTime: '',
            },
            // 登陆用户信息
            user: {},
            jiaolianYuyueYesnoTypesOptions: [],
            jiaolianYuyueYesnoTypesIndex : 0,
            jiaolianOptions: [],//教练
            jiaolianIndex : 0,//教练下标
            yonghuOptions: [],//用户
            yonghuIndex : 0,//用户下标

        }
        },
        components: {
            wPicker
        },
        computed: {
            baseUrl() {
                return this.$base.url;
            },
        },
        async onLoad(options) {
            let jiaolianParams = {
                page: 1,
                limit: 100,
            }
            let jiaolianData = await this.$api.page(`jiaolian`, jiaolianParams);
            this.jiaolianOptions = jiaolianData.data.list;
            let yonghuParams = {
                page: 1,
                limit: 100,
            }
            let yonghuData = await this.$api.page(`yonghu`, yonghuParams);
            this.yonghuOptions = yonghuData.data.list;
		/*下拉框*/
			let jiaolianYuyueYesnoTypesParams = {
                page: 1,
                limit: 100,
                dicCode: 'jiaolian_yuyue_yesno_types',
            }
			let jiaolianYuyueYesnoTypes = await this.$api.page(`dictionary`, jiaolianYuyueYesnoTypesParams);
			this.jiaolianYuyueYesnoTypesOptions = jiaolianYuyueYesnoTypes.data.list


            // 如果是更新操作
            if (options.id) {
                this.ruleForm.id = options.id;
                // 获取信息
                let res = await this.$api.info(`jiaolianYuyue`, this.ruleForm.id);
                this.ruleForm = res.data;
            }
            if(options.jiaolianYuyueId){
                this.ruleForm.jiaolianYuyueId = options.jiaolianYuyueId;
            }
            // 跨表
            // this.styleChange()
        },
        methods: {
            jiaolianChange(e) {
                this.jiaolianIndex = e.target.value
                this.ruleForm.jiaolianName = this.jiaolianOptions[this.jiaolianIndex].jiaolianName
                this.ruleForm.jiaolianId = this.jiaolianOptions[this.jiaolianIndex].id
            },
            yonghuChange(e) {
                this.yonghuIndex = e.target.value
                this.ruleForm.yonghuName = this.yonghuOptions[this.yonghuIndex].yonghuName
                this.ruleForm.yonghuId = this.yonghuOptions[this.yonghuIndex].id
            },
            // 下拉变化
            jiaolianYuyueYesnoTypesChange(e) {
                this.jiaolianYuyueYesnoTypesIndex = e.target.value
                this.ruleForm.jiaolianYuyueYesnoValue = this.jiaolianYuyueYesnoTypesOptions[this.jiaolianYuyueYesnoTypesIndex].indexName
                this.ruleForm.jiaolianYuyueYesnoTypes = this.jiaolianYuyueYesnoTypesOptions[this.jiaolianYuyueYesnoTypesIndex].codeIndex
            },
            // styleChange() {
            // 	this.$nextTick(() => {
            // 		// document.querySelectorAll('.app-update-pv .cu-form-group .uni-yaoxianStyle-yaoxianStyle').forEach(el=>{
            // 		//   el.style.backgroundColor = this.addUpdateForm.yaoxianStyle.content.backgroundColor
            // 		// })
            // 	})
            // },
			// 日期控件
			jiaolianYuyueTimeConfirm(val) {
                this.ruleForm.jiaolianYuyueTime = val.result;
                this.$forceUpdate();
            },
			// 日期控件
			insertTimeConfirm(val) {
                this.ruleForm.insertTime = val.result;
                this.$forceUpdate();
            },
			// 日期控件
			createTimeConfirm(val) {
                this.ruleForm.createTime = val.result;
                this.$forceUpdate();
            },

            getUUID() {
                return new Date().getTime();
            },
            async onSubmitTap() {
				if ((!this.ruleForm.jiaolianYuyueTime)) {
                    this.$utils.msg(`预约时间不能为空`);
                    return
                }
				if ((!this.ruleForm.jiaolianYuyueText)) {
                    this.$utils.msg(`预约备注不能为空`);
                    return
                }
				
				if(uni.getStorageSync('jiaolianId')){
					this.ruleForm.jiaolianId = uni.getStorageSync('jiaolianId')
					uni.removeStorageSync('jiaolianId')
				}
                if (this.ruleForm.id) {
                    await this.$api.update(`jiaolianYuyue`, this.ruleForm);
                } else {
                    await this.$api.save(`jiaolianYuyue`, this.ruleForm);
                }
                uni.setStorageSync('pingluenStateState', true);
                this.$utils.msgBack('提交成功');
            },
            getDate(type) {
                const date = new Date();
                let year = date.getFullYear();
                let month = date.getMonth() + 1;
                let day = date.getDate();
                if (type === 'start') {
                    year = year - 60;
                } else if (type === 'end') {
                    year = year + 2;
                }
                month = month > 9 ? month : '0' + month;;
                day = day > 9 ? day : '0' + day;
                return `${year}-${month}-${day}`;
            },
            toggleTab(str) {
                this.$refs[str].show();
            }
        }
    }
</script>
<style lang="scss" scoped>
    .container {
        padding: 20upx;
    }

    .content:after {
        position: fixed;
        top: 0;
        right: 0;
        left: 0;
        bottom: 0;
        content: '';
        background-attachment: fixed;
        background-size: cover;
        background-position: center;
    }

    textarea {
        border: 1upx solid #EEEEEE;
        border-radius: 20upx;
        padding: 20upx;
    }

    .title {
        width: 180upx;
    }

    .avator {
        width: 150upx;
        height: 60upx;
    }

    .right-input {
        flex: 1;
        text-align: left;
        padding: 0 24upx;
    }

    .cu-form-group.active {
        justify-content: space-between;
    }

    .cu-form-group .title {
        height: auto;
        line-height:30rpx
    }

    .btn {
        display: flex;
        align-items: center;
        justify-content: center;
        flex-wrap: wrap;
        padding: 20upx 0;
    }

    .cu-form-group {
        padding: 0 24upx;
        background-color: transparent;
        min-height: inherit;
    }

    .cu-form-group+.cu-form-group {
        border: 0;
    }

    .cu-form-group uni-input {
        padding: 0 30upx;
    }

    .uni-input {
        padding: 0 30upx;
    }

    .cu-form-group uni-textarea {
        padding: 30upx;
        margin: 0;
    }

    .cu-form-group uni-picker::after {
        line-height: 80rpx;
    }

    .select .uni-input {
        line-height: 80rpx;
    }

    .input .right-input {
        line-height: 88rpx;
    }
</style>