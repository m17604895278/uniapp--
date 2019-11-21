<template>
	<!-- <view class="watefall"> -->
	<view class="pubu" >
		<view class="yg fl" style="margin-bottom: 100upx; background-color: #F1F1F1;" id="leftview" :leftheight="0">
			<leftWaterfall :item="item" :index="index" :colId="1" :imgW="item.info.width" :columnWidth="columnWidth" :imgH="item.info.height" v-for="(item,index) in leftList"></leftWaterfall>
			<!-- :columnWidth="columnWidth" :img_size="item.info" -->
		</view>
		 <view class="yg fr" style="margin-bottom: 100upx; background-color: #F1F1F1;" id="rightview" :rightheight="0">
		 	<leftWaterfall :item="item" :index="index" :colId="2" :imgW="item.info.width" :columnWidth="columnWidth" :imgH="item.info.height" v-for="(item,index) in rightList"></leftWaterfall>
		 </view>
	
	</view>
	<!-- </view> -->
</template>

<script>
	import leftWaterfall from "./left/left-waterfall.vue"
	export default{
		components: {
			leftWaterfall,
		},
		props: {
			list: {
				type: Array,
				default: []
			},
			columnNum:{
				type:Number,
				default:1
			},
			isinit:{
				type: Boolean,
				default: false
			}
			
		},
		data() {
			return {
				page:1,
				pageMax:1,
				// 左右数据
				leftList:[],
				rightList:[],
				leftheight:0,
				rightheight:0,
				penddingList:[],
				positionId:1,
				data:[],
				heightInfo:0,
				columnWidth:175
			}
		},
		watch: {
			list(newValue, oldValue) {
				this.initHeight()
				this.getcolumninfo()
				// console.log("数据发生变化")
				this.setdata(newValue,2);
			}
		},
		
		created() {
			uni.$on('itemRender', (val) => {
				// console.log(val)
				this.positionId = val.colId
				this.heightInfo = val.height
				this.setdata()
				
			})
			
			
		},
		onHide() {
			console.log(89899)
		},
		methods: {
			setdata(arr=null,type=1){
				// console.log('type:'+type)
				if(arr && arr!=null){
					this.data = this.data.concat(arr) 
				}
				if(type == 1){
				if(this.positionId ==1){
					this.leftheight +=this.heightInfo
				}else if(this.positionId ==2){
					this.rightheight +=this.heightInfo
				}
				}
				
				// console.log('左侧'+this.leftheight, this.leftList.length)
				// console.log('右侧'+this.rightheight, this.rightList.length)
				// console.log('length侧'+this.data.length)
				if(this.data.length>0){
					if(this.leftheight > this.rightheight){
						// console.log('右侧+++++')
						this.rightList = this.rightList.concat(this.data.shift());
					}else{
						// console.log('左侧+++++')
						this.leftList =  this.leftList.concat(this.data.shift());
					}
				}
			},
			// 获取一列的宽度，用于计算图片的高度
			getcolumninfo(){
				const that = this;
				const query = uni.createSelectorQuery().in(this);
				query.select('#leftview').boundingClientRect(data => {
					that.columnWidth = data.width
					// console.log(data)
				}).exec();
			},
			// 初始化高度-说明是新的页面展示
			initHeight(){
				if(this.isinit){
					this.leftheight=0;
					this.rightheight=0;
				}
			}
		},
	}
	
</script>

<style lang="scss">
	@import url("../../colorui/main.css");
	// .watefall{
		.pubu {
			width: calc(100% - 16px);
			margin-left: 8px;
		}
		
		.yg .img {
			width: 100%;
			border-radius: 8px 8px 0 0;
		}
		
		.yg .p {
			width: 100%;
			font-size: 0.75rem;
			padding: 3px;
			overflow: hidden;
		/* 	text-overflow: ellipsis;
			white-space: nowrap; */
			
			display: -webkit-box;
			-webkit-line-clamp: 3;
			-webkit-box-orient: vertical;
			overflow: hidden;
			color: #999;
		}
		
		.yg_l,
		.yg_r {
			width: calc(50% - 5px);
		}
		
		.yg .li {
			background: #fff;
			border-radius: 8px;
			margin-bottom: 10px;
			box-shadow: 0 0 5px #999;
		}
		
		.yg_l {
			float: left;
		}
		
		.yg_r {
			float: right;
		}
		.height65 {
			height: 65%;
		}
	// }
	
</style>
