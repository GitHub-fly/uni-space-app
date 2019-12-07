<template>
	<view class="container">
		<view class="sy-input-box box-height-c">
			<label>{{ mobileTip }}</label>
			<input type="text" class="sy-input" placeholder="手机号" maxlength="11" v-model="userDto.name" required="required" @input="checkPhone()" />
			<view class="group-row-center">
				<input type="text" class="sy-input-small" placeholder="验证码" maxlength="6" v-model="userDto.verifyCode" required="required" />
				<button class="btn" @tap="getVerifyCode()" :disabled="status">获取验证码</button>
			</view>
			<view class="text-end title-sub">
				<navigator url="/pages/register/register"><text>还没账号去注册</text></navigator>
			</view>
		</view>
		<button class="btn-big" @tap="signIn(userDto)">登录</button>
		<view class="text">
			<navigator url="/pages/sign_a/sign_a"><text>其他方式登录</text></navigator>
		</view>
	</view>
</template>

<script>
var codes;
var mymobile;
import uniCountdown from '@/components/uni-countdown/uni-countdown.vue';
export default {
	comments: {
		uniCountdown
	},
	data() {
		return {
			seconds: 10,
			codeBtn: {
				text: '获取验证码',
				waitingCode: false,
				count: this.seconds
			},
			mobileTip: '',
			userDto: {
				name: '',
				verifyCode: ''
			},
			status: true
		};
	},
	methods: {
		sendCode: function() {
			this.codeBtn.waitingCode = true;
			this.codeBtn.count = this.seconds;
			this.codeBtn.text = this.codeBtn.count + 's';

			let countdown = setInterval(() => {
				this.codeBtn.count--;
				this.codeBtn.text = this.codeBtn.count + 's';
				if (this.codeBtn.count < 0) {
					clearInterval(countdown);
					this.codeBtn.text = '重新发送';
					this.codeBtn.waitingCode = false;
				}
			}, 1000);
		},

		getVerifyCode: function() {
			uni.request({
				url: this.$baseUrl + '/sms',
				method: 'POST',
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				data: {
					mobile: this.userDto.name
				},
				success: res => {
					this.user = res.data.data;
					if (res.data.code === 1) {
						uni.showToast({
							title: '验证码发送成功'
						});
						(codes = res.data.data), console.log(codes);
						mymobile = this.userDto.name;
					} else {
						uni.showModal({
							title: '验证码发送失败'
						});
					}
				}
			});
		},
		signIn(userDto) {
			uni.request({
				url: this.$baseUrl + '/user/sign_in',
				method: 'POST',
				header: {
					'content-type': 'application/json'
				},
				data: {
					name: userDto.name,
					verifyCode: codes
				},
				success: res => {
					if (res.data.code === 1) {
						uni.showToast({
							title: res.data.msg
						}),
							uni.navigateTo({
								url: '/pages/my_info/my_info?user=' + encodeURIComponent(JSON.stringify(this.user))
							});
					} else {
						uni.showModal({
							title: '提示',
							content: res.data.msg
						});
					}
				}
			});
		},
		checkPhone() {
			var phone = this.userDto.name;
			if (!/^1[3456789]\d{9}$/.test(phone)) {
				this.mobileTip = '手机格式不正确';
				this.status = true;
				return false;
			} else {
				this.mobileTip = '';
				this.status = false;
			}
		}
	}
};
</script>

<style scoped>
label {
	color: #f44336;
	text-align: center;
	font-size: 12px;
}
</style>
