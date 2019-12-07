<template>
	<view class="container">
		<view class="sy-input-box box-height-h">
			<input type="text" class="sy-input" placeholder="手机号" v-model="userDto.name" maxlength="11" />
			<view class="group-row-center">
				<input type="text" class="sy-input-small" placeholder="验证码" maxlength="6" v-model="verifyCode" />
				<button class="btn" @tap="getVerifyCode()">获取验证码</button>
			</view>
			<input type="password" class="sy-input" v-model="userDto.password" placeholder="设置新的密码" />
			<input type="password" class="sy-input" v-model="r_password" placeholder="再次确认密码" />
		</view>
		<button class="btn-big" style="margin-top: 10%;" @tap="froget(userDto)">确认</button>
	</view>
</template>

<script>
export default {
	data() {
		return {
			userDto: {
				name: '',
				password: ''
			},
			verifyCode: '',
			r_password: ''
		};
	},
	onLoad() {},
	methods: {
		getVerifyCode: function() {
			uni.request({
				url: this.$baseUrl + '/sms?mobile=' + this.userDto.name,
				method: 'POST',
				header: {
					'content-type': 'application/x-www-form-urlencoded'
				},
				data: {
					mobile: this.userDto.name
				},
				success: res => {
					if (res.data.code === 1) {
						uni.showToast({
							title: '验证码发送成功'
						});
						(code = res.data.data), console.log(code), (mymobile = this.userDto.name);
					} else {
						uni.showModal({
							title: '验证码发送失败'
						});
					}
				}
			});
		},
		froget: function(userDto) {
			uni.request({
				url: this.$baseUrl + '/user/UserPassword',
				method: 'PUT',
				header: {
					'content-type': 'application/json'
				},
				data: {
					name: this.userDto.name,
					password: this.userDto.password,
					verifyCode: this.verifyCode
				},
				success: res => {
					if (res.data.code === 1) {
						uni.showToast({
							title: res.data.data
						});
					}
				}
			});
		},
		checkPhone(){
			var phone =this.userDto.name;
			    if(!(/^1[3456789]\d{9}$/.test(phone))){
					this.mobileTip ='手机格式不正确';
					this.status = true;
			        console.log('aaa')
			        return false;
			    } else{
					this.mobileTip ='';
					this.status = false;
				}
		}
	}
};
</script>

<style></style>
