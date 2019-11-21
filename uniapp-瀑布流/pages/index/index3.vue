<template>
	<view>
		<!-- 解决顶部与手机状态栏冲突 -->
		<view class="status_bar">  
			<!-- <view class="top_view"></view>  -->
		</view> 
		<!-- 头部搜索 -->
		<view class="cu-bar bg-white justify-center" >
			<!--padding-tb align-end style="height:80px;" -->
			<navigator class="text-lg text-black" style="width: 20%;" url="/pages/zhengzhuang/index/city_list">
				<!-- 如加搜索框请加 margin-left 类-->
				<text class="text-hiden" style="width: 88%; display: block;">{{city}}</text>
				<!-- <text class="cuIcon-triangledownfill"></text> -->
			</navigator>
			<!-- 搜索框 -->
			<!-- <view class="search-form round">
					<text class="cuIcon-search"></text>
					<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" type="text" value="" placeholder="搜索你喜欢的楼盘" confirm-type="search"></input>
				</view> -->
			<!-- #ifdef APP-PLUS -->
			<view class="bg-ss text-gray padding-xs flex justify-between align-center round margin-sm" style="width: 60%;">
				<text class="cuIcon-search margin-right-xs" @click="toSearch()"></text>
				<input class="flex-sub text-black" @confirm="toSearch()" type="text" placeholder="搜索文章标题" placeholder-class="text-gray" v-model="keyword"
				 confirm-type="search"></input>
			</view>
			<!-- #endif -->
			<!-- #ifdef H5 -->
			<view class="bg-ss text-gray padding-xs flex justify-between align-center round margin-sm" style="width: 68%;">
				<text class="cuIcon-search margin-right-xs" @click="toSearch()"></text>
				<input class="flex-sub text-black" @confirm="toSearch()" type="text" placeholder="搜索文章标题" placeholder-class="text-gray" v-model="keyword"
				 confirm-type="search"></input>
			</view>
			<!-- #endif -->
			<!-- #ifdef APP-PLUS -->
			<view>
				<view class="scan-r" @click="scan()" style="width: 10%;">
					&#xe619;
					<!-- <text>扫一扫</text> -->
				</view>
			</view>
			<!-- #endif -->
		</view>
		
		<view>
			<!-- <view class="pubu" >
				<view class="yg fl" style="margin-bottom: 100upx; background-color: #F1F1F1;" id="leftview">
					<view class="radius10 margin-bottom-sm bg-white" v-for="(item,index) in leftList" :key="index"   @click="articleDetail(item.id,index,item.type)">
						<image mode="widthFix" class="imgradius10 block response" :src="item.cover" alt="" />
						<view class="padding-sm">
							<view class="margin-bottom-xs height65 text-justify">
								{{item.title}}
							</view>
							<view class="flex justify-between align-center margin-top-xs text-sm">
								<view class="flex align-center">
									<view class="cu-avatar sm round overflowhidden">
										<image :src="'../../static/user_img/'+index+'.jpg'" mode='widthFix' class="img"></image>
									</view>
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
				</view>
				 <view class="yg fr" style="margin-bottom: 100upx; background-color: #F1F1F1;" id="rightview">
				 	<view class="radius10 margin-bottom-sm bg-white" v-for="(item,index) in rightList" :key="index"  @click="articleDetail(item.id,index,item.type)">
				 		<image mode="widthFix" class="imgradius10 block response" :src="item.cover" alt="" />
				 		<view class="padding-sm">
				 			<view class="margin-bottom-xs height65 text-justify">
				 				{{item.title}}
				 			</view>
				 			<view class="flex justify-between align-center margin-top-xs text-sm">
				 				<view class="flex align-center">
				 					<view class="cu-avatar sm round overflowhidden">
				 						<image :src="'../../static/user_img/'+index+'.jpg'" mode='widthFix' class="img"></image>
				 					</view>
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
				 </view>

			</view> -->
		<watefall :list="penddingList"  />
		</view>
	</view>
</template>
<script>
	import watefall from '@/components/waterfall/index.vue'
	export default {
		components: {
			watefall
		},
		data() {
			return {
				city: '北京市',
				city_id: 0,
				keyword:'',
				loading: false,
				TabCur: 0,
				scrollLeft: 0,
				// list: {'0':'精选','1':'热门','2':'推荐'},
				prolist: [],
				swiperList: [],
				// ListData: [],
				imgList: [],
				page:1,
				pageMax:1,
				// 左右数据
				leftList:[],
				rightList:[],
				leftheight:0,
				rightheight:1,
				penddingList:[],
			};
		},
		onShow() {
			var _this = this;
			// console.log(123)
			this.getcity(); 
		
		},
		onLoad: function() {
		 this.getList();
		},
		onReachBottom() {
			this.page+=1;
			// console.log(this.page);
			this.getList(0,0,'',this.page);
		},
		methods: {
			tabSelect(e) {
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60
			},
			getList: function(is_hot=0,is_new=0,keyword='',page=1) {
				if(this.page>this.pageMax){
					if(keyword == ''){
						console.log('没有数据了')
						return;
					}
				}
				uni.showLoading({
					title:'加载中...'
				})
				//imgList API返回的参数
				let _the = this
				uni.request({
					url: 'https://cms.bpbzx.com/cms/api/xhsInfo',
					// url: 'https://lxj.nat.bpbzx.com/index.php/cms/api/catInfo',
					data: {
						id: '16',
						// 暂无
						is_hot,
						is_new,
						keyWord:keyword,
						page
					},
					method: 'GET',
					success: function(e) {
						uni.stopPullDownRefresh();
						let res = e.data
						if (res.code == 0) {
							_the.penddingList = res.data.lists;
							_the.pageMax = res.data.last_page;
							// _the.getsysinfo();
							uni.hideLoading();
						} else {
							uni.showToast({
								title: '网络错误！',
								icon: 'none',
								duration: 1500,
								mask: !0
							});
						}
					},
					fail() {
						uni.showToast({
							title: '网络错误！',
							icon: 'none',
							duration: 1500,
							mask: !0
						});
						uni.stopPullDownRefresh();
					}
				})
			},
			
			
		},
	}
</script>

<style>
	@import url("../../colorui/main.css");
	/* @import url("../../static/css/style.css"); */
	/* @import url("../../common/icon.css"); */
	@font-face {
	  font-family: 'saoyisao';  /* project id 1311213 */
	  src: url('http:///at.alicdn.com/t/font_1311213_sdeslgj65x.eot');
	  src: url('http:///at.alicdn.com/t/font_1311213_sdeslgj65x.eot?#iefix') format('embedded-opentype'),
	  url('http:///at.alicdn.com/t/font_1311213_sdeslgj65x.woff2') format('woff2'),
	  url('http:///at.alicdn.com/t/font_1311213_sdeslgj65x.woff') format('woff'),
	  url('http:///at.alicdn.com/t/font_1311213_sdeslgj65x.ttf') format('truetype'),
	  url('http://at.alicdn.com/t/font_1311213_sdeslgj65x.svg#iconfont') format('svg');
	}

	page {
		background: #F1F1F1;
	}
	.status_bar {  
		height: var(--status-bar-height);  
		width: 100%;  
		background-color: #FFF;
	} 
	.text-hiden{
		overflow: hidden;
		text-overflow:ellipsis;
		white-space: nowrap;
	}
	.big-img {
		height: 400upx;
	}

	.cu-avatar {
		background: none;
	}

	.cu-list.grid.no-border>.cu-item {
		padding-top: 20upx;
		padding-bottom: 0upx
	}

	.cu-list.grid {
		background: none;
	}

	.scan-r{
		width: 40upx;
		height: 40upx;
		/* position: absolute; */
		/* top:35%; */
		/* right:6%; */
		font-family: saoyisao;
		font-size: 30upx;
		/* color: #000000; */
	}
	.height_width {
		height: 8upx;
		width: 17%;
	}

	.text-sy {
		color: #ed6c69;
	}

	.bg-syred {
		background: #ed6c69;
	}

	.grid.grid-square>view {
		border-radius: 0upx;
	}

	.cu-btn.sm {
		padding: 0 20upx;
		font-size: 24upx;
		height: 35upx;
	}

	.height65 {
		height: 65%;
	}



	.res {
		position: relative;
	}

	.posi {
		position: absolute;
		bottom: 30%;
		width: 20%;
		height: 10upx;
		left: 25%;
		z-index: -1;
		background: #ed6c69;
	}

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

	.u_xinx {
		position: absolute;
		bottom: 4px;
	}

	.u_xinx .img {
		margin-left: 5px;
		background: rgba(0, 0, 0, .2);
		box-shadow: 0 1px 3px rgba(0, 0, 0, .5);
		width: 26px;
		height: 26px;
		border-radius: 50%;
		display: inline-block;
	}

	.u_xinx .span {
		position: relative;
		text-shadow: 0 1px 1px #333;
		top: -8px;
		color: #fff;
		font-size: 0.6rem;
	}
	.flex {
		display: flex;
	}
</style>
