<template>
	<view class="container">
		<view class="sy-input-box box-height-a">
			<input type="number" placeholder="请输入手机号/账号/邮箱" class="sy-input" v-model="userDto.name" maxlength="11" />
			<view class="nowrap sy-center"><input type="password" placeholder="请输入密码" class="sy-input" v-model="userDto.password" /></view>
			<view class="select group-row-between">
				<navigator url="/pages/register/register"><text class="title-sub">快速注册</text></navigator>
				<navigator url="/pages/forget_p/forget_p"><text class="title-sub">找回密码</text></navigator>
			</view>
			<button class="r-button" @tap="signIn(userDto)">登录</button>
		</view>
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
			user: {}
		};
	},
	onLoad() {},
	methods: {
		signIn: function(userDto) {
			uni.request({
				url: this.$baseUrl + '/user/sign_in',
				method: 'POST',
				data: {
					name: userDto.name,
					password: userDto.password
				},
				header: {
					'content-type': 'application/json'
				},
				success: res => {
					this.user = res.data.data;
					console.log(this.user);
					if (res.data.code === 1) {
						uni.showToast({
							title: '登录成功'
						});
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
		}
	}
};
</script>

<style scoped>
.r-button {
	width: 100%;
}
.sy-input-box {
	margin-top: 25%;
}
.sy-input {
	height: 110upx;
}
button::after {
	border: none;
}
.select {
	margin-top: 10%;
	margin: 0 auto;
	width: 90%;
}
</style>
