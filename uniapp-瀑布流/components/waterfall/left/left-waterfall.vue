<template>
	<!-- <view class="left"> -->
		<view class="radius10 margin-bottom-sm bg-white" :id="'item_'+colId+'_'+index"  :key="index"   @click="articleDetail(item.id,index,item.type)">
			<image mode="widthFix" class="imgradius10 block response" :src="item.cover" alt="" />
			<view class="padding-sm">
				<view class="margin-bottom-xs height65 text-justify">
					{{item.title}}
				</view>
				<view class="flex justify-between align-center margin-top-xs text-sm">
					<view class="flex align-center">
						<image :src="'../../static/user_img/'+index+'.jpg'" mode='widthFix' class="response block cu-avatar sm round"></image>
						<view class="margin-left-xs basis-70">
							{{item.author}}
						</view>
					</view>
					<view class="basis-sm text-right">
						<text v-if="item.e_type==0" class="cuIcon-likefill text-red margin-right-xs"></text>
						<text v-else class="cuIcon-attention text-gray margin-right-xs"></text>
						{{item.view}}
					</view>
				</view>
			</view>
		</view>
	<!-- </view> -->
</template>

<script>
	export default{
		props: {
			item: {
				type: Object,
				default: []
			},
			index:{
				type:Number,
				default:'1'
			},
			colId:{
				type:Number,
				default:'1'
			},
			columnWidth:{
				type:Number,
				default:0
			},
			// img_size:{
			// 	type:Array,
			// 	default:[]
			// },
			imgW:{
				type:Number,
				default:0
			},
			imgH:{
				type:Number,
				default:0
			},
		},
		data() {
			return {
				lefthright: 0,
				list:[],
				// img_width:'0',
				img_height:'0'
			}
		},
		watch: {
			item(newValue, oldValue) {
				// this.getElHeight()
			}
		},
		mounted(){
			// this.$on('lefthright', val => {
			// 	this.lefthright = val
			// })
			let that = this;
			// 计算图片高度
			// 计算文字高度
			// 返回该元素的正常加载高度
			// 固定图片宽高
			// this.$nextTick(() => {
				// setTimeout(that.ces, 200)
				that.ces()
				
			// })
		},
		methods: {
			ces() {
				const that = this;
				that.getElHeight()
				const query = uni.createSelectorQuery().in(this);
				query.select('#item_'+this.colId+'_'+this.index).boundingClientRect(data => {
				// let height = data.height + that.img_height;
				  uni.$emit('itemRender', {colId: that.colId, height: that.img_height})
				  // that.$parent.$parent.$parent.$emit('colId', that.colId)
				  // console.log("节点离页面顶部的距离为" + data.top);
				}).exec();
			},
			// 跳转文章详情
			articleDetail(val,index,type=0) {
				let url = '/pages/article/detail-2?id=' + val+'&index=' +index
				if(type==1){
					// 视频跳转
					url ='/pages/dy/video?id=' + val +'&index=' + index
				}
				
				this.$common.navigateTo(url);
			},
			// 计算图片高度，并返回该元素的高度
			getElHeight(){
				let _the = this
				let imageW = _the.imgW
				let imageH = _the.imgH
				let img_ratio = imageW/_the.columnWidth
				let img_ratio_height = imageH/img_ratio
				_the.img_height = img_ratio_height
				// console.log(img_ratio_height)
				// 默认图片高度240
			}
			// setdata(arr){
			// 	this.list = this.leftList
			// }
		},
	}
</script>

<style>
	@import url("../../../colorui/main.css");
</style>
