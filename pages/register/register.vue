<template>
	<view class="container">
		<view class="sy-input-box">
			<label>{{mobileTip}}</label>
			<input type="number" placeholder="请输入手机号" class="sy-input" v-model="userDto.mobile" required="required" maxlength="11" @input="checkPhone()"/>
			<view class="yan nowrap">
				<input type="text" placeholder="输入验证码" class=" sy-input-small" v-model="userDto.verifyCode" required="required" />
				<button class="btn" @tap="getVerifyCode(), checkPhone()" :disabled="status">获取验证码</button>
			</view>
			<input type="password" placeholder="6-20位字母数字密码" class="sy-input" v-model="userDto.password" />
			<input type="password" placeholder="请确认密码" class="sy-input" v-model="re_password" />
		</view>
		<button class="r-button" @tap="signUp()">注册</button>
	</view>
</template>

<script>
	var codes, ps,reps,mymobile;
	import uniCountdown from '../../components/uni-countdown/uni-countdown.vue'
export default {
	comments:{
		uniCountdown
	},
	data() {
		return {
			re_password: '',
			status: true,
			mobileTip:'',
			userDto:{
			mobile: '',
			verifyCode: '',
			password:''	
			}
			
		};
	},
	onLoad() {},
	methods: {
		getVerifyCode: function() {
			
			uni.request({
				url: this.$baseUrl + '/sms',
				method: 'POST',
				header: { 'content-type': 'application/x-www-form-urlencoded' },
				data: {
					mobile: this.userDto.mobile
				},
				success: res => {
					console.log(res.data.data);
					if (res.data.code === 1) {
						uni.showToast({
							title: '验证码已发送'
						});
						codes = res.data.code
						reps = this.re_password
						ps = this.userDto.password
						mymobile = this.userDto.name
					} else {
						uni.showToast({
							title: '验证码发送失败'
						});
					}
				}
			});
		},
		checkPhone(){
			var phone =this.userDto.mobile
			    if(!(/^1[3456789]\d{9}$/.test(phone))){ 
					this.mobileTip ='手机格式不正确';
					this.status = true;
			        return false; 
			    } else{
					this.mobileTip =''; 
					this.status = false;
				}
		},
		signUp: function(userDto) {
			uni.request({
				url: this.$baseUrl + '/user/sign_up',
				method: 'POST',
				header: {
					'content-type': 'application/json'
				},
				data:{
					name:this.userDto.mobile,
					verifyCode:this.userDto.verifyCode,
					password:this.userDto.password
				},
				success: res => {
					if (res.data.code === 1){
						uni.showModal({
							title: '提示',
							content: '注册成功'
						}); 
						uni.navigateTo({
							url: '../sign_a/sign_a'
						});
					} else {
						uni.showModal({
							title: '提示',
							content: res.data.msg
						});
					}
				}
			});
		}
	}
};
</script>

<style scoped>
.sy-input-box {
	height: 500upx;
	margin-top: 18%;
}
.yan {
	width: 100%;
}
label{
	color: #f44336;
}
</style>
