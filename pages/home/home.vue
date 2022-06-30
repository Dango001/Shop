<template>
	<!-- SWIPER AREA -->
	<view>
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item, i) in swiperList" :key="i">
				<navigator class="swiper-item" :url="'/subpackages/goods-details/goods-details?goods_id='+item.goods_id">
					<image :src="item.image_src" mode=""></image>
				</navigator>
			</swiper-item>
		</swiper>

	
	<!-- CATEGORIES NAVIGATION AREA -->
	<view class="nav-list">
		<view class="nav-item" v-for="(item,index) in navList" :key="index" 
		@click="navClickHandler(item)">
			<image class="nav-img" :src="item.image_src" mode=""></image>
		</view>
		
	</view>
	
	<!-- FLOOR LIST AREA -->
	<view class="floor-list">
		<view class="floor-item" v-for="(item, index) in floorList" :key="index">
			<image :src="item.floor_title.image_src" mode="" class="floor-title"></image>
			<view class="floor-img-box">
				<navigator class="left-img-box" :url="item.product_list[0].url">
					<image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width + 'rpx'}" mode="widthFix"></image>
				</navigator>
				<view class="right-img-box">
					<navigator :url="item1.url" class="right-img-item" v-for="(item1, index1) in item.product_list" :key="index1" v-if="index1!==0">
						<image :src="item1.image_src" mode="widthFix" :style="{width:item1.image_width +'rpx'}"></image>
					</navigator>
					
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
				swiperList:[],
				navList:[],
				floorList:[]
			};
			
		},
		onLoad() {
			this.getSwiperList(),
			this.getNavList(),
			this.getFloorList()
		},
		methods:{
			async getSwiperList(){
				const res = await uni.$http.get('/api/public/v1/home/swiperdata')
				console.log(res.data.message)
				if(res.data.meta.status!==200) return uni.$showMsg()
				this.swiperList = res.data.message
			},
			async getNavList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/catitems')
				if(res.meta.status!==200) return uni.$showMsg()
				this.navList = res.message
			},
			navClickHandler(item){
				if(item.name==='分类'){
					uni.switchTab({
						url:'/pages/cate/cate'
					})
				}
			},
			async getFloorList(){
				const {data: res} = await uni.$http.get('/api/public/v1/home/floordata')
				if(res.meta.status!==200) return uni.$showMsg()
				res.message.forEach(floor=>{
					floor.product_list.forEach(prod=>{
						prod.url = '/subpackages/goods-list/goods-list?' + prod.navigator_url.split('?')[1]
					})
				})
				
				this.floorList = res.message
			}
		}
	}
</script>

<style lang="scss">
swiper{
	height: 330rpx;
	.swiper-item,
	image{
		width: 100%;
		height: 100%;
	}
}
.nav-list{
	display: flex;
	justify-content: space-around;
	margin: 15px 0;
	
	.nav-img{
		height: 140rpx;
		width: 128rpx;
	}
}
.floor-title{
	height: 60rpx;
	width: 100%;
}

.floor-img-box{
	display: flex;
	padding-left: 10rpx;
}

.right-img-box{
	display: flex;
	flex-wrap: wrap;
	justify-content: space-around;
}


</style>
