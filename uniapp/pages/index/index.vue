<template>
	<view>
		<button type="default" @click="updataimg">选择图片</button>
		<view class="">
			<view class="">
				<view v-for="(image, index) in imageList" :key="index">
					<view class="">
						<view class="" @click="onDeleteClick(index)">
							<icon type="clear" size="26" />
						</view>
						<image class="" :src="image" :data-src="image" @tap="previewImage">
						</image>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				imageList: []
			}
		},
		methods: {
			//上传
			updataimg() {
				const that = this
				uni.chooseImage({
					count: 1, // 最多可以选择的图片张数，默认9
					sizeType: ['original', 'compressed'], //original 原图，compressed 压缩图，默认二者都有
					sourceType: ['album',
						'camera'
					], //album 从相册选图，camera 使用相机，默认二者都有。如需直接开相机或直接选相册，请只使用一个选项
					success: function(res) {
						console.log(res.tempFilePaths[0])
						const uploadTask = uni.uploadFile({
							url: 'http://127.0.0.1:8081/fileUpload', //具体后台接入地址
							filePath: res.tempFilePaths[0],
							name: 'file',
							success: function(uploadFileRes) {
								console.log(uploadFileRes);
								let imageTempObj = JSON.parse(uploadFileRes.data);
								console.log(imageTempObj);
								_this.imgList = [..._this.imgList, imageTempObj.result]
							}
						});

						const len = that.imageList.length
						if (len >= 1) {
							uni.showToast({
								title: '一次只能上传1张'
							})
						} else {
							for (let i = 0; i < 6 - len; i++) {
								if (res.tempFilePaths[i]) that.imageList.push(res
									.tempFilePaths[i])
							}
						}
					}
				})
			},
			//照片删除
			onDeleteClick(index) {
				this.imageList.splice(index, 1)
			},
			//照片的预览
			previewImage(e) {
				let imgArr = []
				imgArr.push(e.currentTarget.dataset.src)
				console.log(imgArr);
				uni.previewImage({
					urls: imgArr
				})
			},
		}
	}
</script>

<style>
</style>
