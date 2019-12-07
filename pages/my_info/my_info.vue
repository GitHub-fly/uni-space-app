<template>
	<view class="container">
		<view class="v-bar">
			<text>返回</text>
			<text @tap="updateUserData(userBefore)">确定</text>
		</view>
		<!-- <image src="../../static/img/dynamic1.png"></image> -->
		<avatar class="s-avatar" selWidth="200px" selHeight="200upx" 
		@upload="myUpload"
		:upImgConfig="upImgOos" @onUpImg="upOosData"
		ref="uImage"
		 :avatarSrc="url" 
		 avatarStyle="width: 200upx; height: 200upx; border-radius: 100%;"></avatar>
		<!-- <sunui-upoos :upImgConfig="upImgOos" @onUpImg="upOosData" @onImgDel="delImgInfo" ref="uImage"></sunui-upoos> -->
		<!-- <button type="primary" @tap="getUpImgInfoOos">获取上传Oos图片信息</button> -->
		<!-- <button type="primary" @tap="uImageTap">手动上传图片</button> -->
		<button @tap="modifyAvatar()">确定</button>
		<view class="s-text">
			<view class="s-box">
				<text class="title-sub">昵称</text>
				<input class="s-input" v-model="userBefore.nickname" />
			</view>
			<view class="s-box">
				<text class="title-sub">简介</text>
				<input class="s-input" v-model="userBefore.introduction" />
			</view>
			<view class="s-box">
				<text class="title-sub">城市</text>
				<input class="s-input" v-model="userBefore.address" />
			</view>
			<view class="s-box">
				<text class="title-sub">性别</text>
				<input class="s-input" v-model="userBefore.gender" />
			</view>
			<view class="s-box">
				<text class="title-sub">星座</text>
				<input class="s-input" v-model="userBefore.constellation" />
			</view>
			<view class="s-box">
				<!-- <text class="title-sub">生日</text>
				<input class="s-input" v-model="userBefore.birthday" /> -->
				<picker mode="region">城市</picker>
			</view>
			<!-- <navigator url="../m_nickname/m_nickname">
				<button class="s-btn-big">
					<view><text class="s-nickname title-sub" @tap="onload()">昵称</text></view>
					<text class="s-nickname">{{nickname}}</text>
					<view>〉</view>
				</button>
			</navigator>
			<navigator url="../m_introduction/m_introduction">
				<button class="s-btn-big">
					<view>
						<text class="s-nickname title-sub">简介</text>
						<text class="s-nickname">{{introduction}}</text>
					</view>
					<view>〉</view>
				</button>
			</navigator>
			<button class="s-btn-big"><pcker class="title-sub s-nickname">性别 {{gender}}</picker></button>
			<button class="s-btn-big"><picker class="title-sub s-nickname">城市 {{address}}</picker></button>
			<button class="s-btn-big"><picker class="title-sub s-nickname">星座{{constellation}}</picker></button>
			<button class="s-btn-big"><picker class="title-sub s-nickname">生日{{birthday}}</picker></button> -->
		</view>
	</view>
</template>

<script>
var aliyunImg;
import avatar from '../../components/yq-avatar/yq-avatar.vue';
export default {
	components: {
		avatar
	},
	data() {
		return {
			url: '',
			userInfo: '',
			userBefore: {
				// nickname: '',
				// introduction: '',
				// gender: '',
				// address: '',
				// birthday: '',
				// constellation: ''
			},
			oosArr: [],
			// 阿里云oos相关配置
			upImgOos: {
				aliConfig: {
					// 阿里云oos上传key_secret(后端传)
					AccessKeySecret: '5iB8rf4vSVl4HmqWOMPIOkAgzmPvI8',
					// 阿里云oos上传key_id(后端传)
					OSSAccessKeyId: 'LTAI4wU5hODbg7FX',
					// 阿里云oos上传目录(必须存在)
					oosDirectory: 'uniapp',
					// 阿里云上传url
					url: 'http://sunui-uniapp.oss-cn-beijing.aliyuncs.com/'
				},
				// 是否开启notli(开启的话就是选择完直接上传，关闭的话当count满足数量时才上传)
				notli: false,
				// 图片数量
				count: 4,
				// 上传图片背景修改
				upBgColor: '#E8A400',
				// 上传icon图标颜色修改(仅限于iconfont)
				upIconColor: '#eee',
				// 上传svg图标名称
				upSvgIconName: 'icon-certificate'
			}
		};
	},
	methods: {
		uImageTap() {
			this.$refs.uImage.uploadimage(this.upImgOos);
		},
		// 删除图片 -2019/05/12(本地图片进行删除)
		async delImgInfo(e) {
			console.log('你删除的图片地址为:', e, this.oosArr.splice(e.index, 1));
		},
		// 阿里云
		async upOosData(e) {
			// 上传图片数组
			let arrImg = [];
			for (let i = 0, len = e.length; i < len; i++) {
				try {
					if (e[i].path_server != '') {
						await arrImg.push(e[i].path_server.split(','));
					}
				} catch (err) {
					console.log('上传失败...');
				}
			}
			// 图片信息保存到data数组
			this.oosArr = arrImg;

			// 可以根据长度来判断图片是否上传成功. 2019/4/11新增
			if (arrImg.length == this.upImgOos.count) {
				uni.showToast({
					title: `上传成功`,
					icon: 'none'
				});
			}
		},
		// 获取上传图片阿里云
		getUpImgInfoOos() {
			console.log('阿里云转成一维数组:', this.oosArr.join().split(',')[0]);
		},

		myUpload(rsp) {
			this.url = rsp.path; //更新头像方式一
			//rsp.avatar.imgSrc = rsp.path; //更新头像方式二
			console.log(this.url);
			uni.request({
				url: this.$baseUrl + '/user/userAvatar',
				method: 'PUT',
				header: {
					'content-type': 'application/json'
				},
				data: {
					id: this.userBefore.id,
					avatar: this.url
				}
			});
			uni.showToast({
				title: '图片修改成功'
			});
		},
		updateUserData: function(userBefore) {
			userBefore = this.userBefore;
			uni.request({
				url: this.$baseUrl + '/user/userData',
				method: 'PUT',
				header: {
					'content-type': 'application/json'
				},
				data: {
					id: userBefore.id,
					address: userBefore.address,
					nickname: userBefore.nickname,
					introduction: userBefore.introduction,
					birthday: userBefore.birthday,
					constellation: userBefore.constellation,
					gender: userBefore.gender
				},
				success: res => {
					if (res.data.code === 1) {
						uni.showToast({
							title: '信息修改成功'
						});
						console.log(userBefore);
						console.log(this.userBefore);
					}
				}
			});
		},
		modifyAvatar: function() {
			console.log(this.url),
				uni.request({
					url: this.$baseUrl + '/user/sign_in',
					method: 'POST',
					data: {
						img: this.url
					},
					header: {
						'content-type': 'application/json'
					},
					success: res => {
						if (res.data.code === 1) {
							console.log(this.url);
							console.log(res.data.data);
						}
						// console.log(res.data.data)
						// if (res.data.code === 1) {
							// uni.request({
							// 	url: this.$baseUrl + '/user/userAvatar',
							// 	method: 'PUT',
							// 	header: {
							// 		'content-type': 'application/json'
							// 	},
							// 	data: {
							// 		id: this.userBefore.id,
							// 		// avatar: aliyunImg
							// 	}
							// });
							// uni.showToast({
							// 	title: '图片修改成功'
							// });
						// }
					}
				});
		}
	},
	onLoad: function(option) {
		//option为object类型，会序列化上个页面传递的参数
		console.log(JSON.parse(decodeURIComponent(option.user)));
		this.userInfo = JSON.parse(decodeURIComponent(option.user));
		this.userBefore = this.userInfo;
		this.url = this.userInfo.avatar;
		// this.user.nickname = this.userInfo.nickname;
		// this.user.introduction = this.userInfo.introduction;
		// this.user.gender = this.userInfo.gender;
		// this.user.address = this.userInfo.address;
		// this.user.constellation = this.userInfo.constellation;
		// this.user.birthday = this.userInfo.birthday;
	}
};
</script>

<style scoped>
.s-avatar {
	width: 100%;
	height: 400upx;
	background: #f4f3de;
	display: flex;
	justify-content: center;
	align-items: center;
}
.s-avatar image {
	width: 100upx;
	height: 100upx;
	border-radius: 50px;
}
.s-text {
	width: 100%;
	margin: 0 auto;
	height: 800upx;
	display: flex;
	flex-direction: column;
	justify-content: space-around;
	align-items: center;
}
.s-box {
	height: 60px;
	display: flex;
	justify-content: space-around;
	align-items: center;
}
.s-input {
	width: 70%;
	height: 45px;
	border-bottom: #777777;
}
.s-btn-big {
	display: flex;
	width: 700upx;
	height: 120upx;
	justify-content: space-between;
	border-bottom: #4cd964;
	align-items: center;
}
.s-nickname {
	margin-left: 20px;
}
.v-bar {
	width: 100%;
	height: 80upx;
	display: flex;
	justify-content: space-around;
	align-items: center;
	background: #e8eaf6;
	color: #9fa8da;
}
</style>
