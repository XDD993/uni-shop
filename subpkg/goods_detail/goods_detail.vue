<template>
	<view>
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item,i) in goods_info.pics" :key="i">
				<image :src="item.pics_big" @click="preview(i)"></image>
			</swiper-item>
		</swiper>
		<!-- 商品区域 -->
		<view class="goods-info-box">
			<!-- 价格区域 -->
			<view class="price">￥{{goods_info.goods_price}}</view>
			<!-- 商品主体区域 -->
			<view class="goods-info-body">
				<!-- 商品名称 -->
				<view class="goods-name">{{goods_info.goods_name}}</view>	
				<!-- 收藏 -->
				<view class="favi">
					<uni-icons type="star" size="18" color="gray"></uni-icons>
					<text>收藏</text>
				</view>
				
			</view>
		<!-- 运费 -->
		<view class="yf">快递：免运费</view>	
		</view>
		<rich-text :nodes="goods_info.goods_introduce"></rich-text>
		<view class="goods_nav">
		  <!-- fill 控制右侧按钮的样式 -->
		  <!-- options 左侧按钮的配置项 -->
		  <!-- buttonGroup 右侧按钮的配置项 -->
		  <!-- click 左侧按钮的点击事件处理函数 -->
		  <!-- buttonClick 右侧按钮的点击事件处理函数 -->
		  <uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="buttonClick" />
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				goods_info:{},
				 options: [{
				      icon: 'shop',
				      text: '店铺'
				    }, {
				      icon: 'cart',
				      text: '购物车',
				      info: 2
				    }],
				    // 右侧按钮组的配置对象
				    buttonGroup: [{
				        text: '加入购物车',
				        backgroundColor: '#ff0000',
				        color: '#fff'
				      },
				      {
				        text: '立即购买',
				        backgroundColor: '#ffa200',
				        color: '#fff'
				      }]
					  
			};
		},
		onLoad(options) {
			const goods_id = options.goods_id
			this.getGoodsDetail(goods_id)
		},
		methods:{
			async getGoodsDetail(goods_id){
				const {data:res}  = await uni.$http.get('/api/public/v1/goods/detail?goods_id=' +goods_id )
				console.log(res)
				if(res.meta.status !== 200) return uni.$showMsg()
				res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g,'<img style = "display:block"' ).replace(/webp/g,'jpg')
				this.goods_info = res.message
			}, 
			preview (i){
				uni.previewImage({
					current:i,
					urls:this.goods_info.pics.map(x => x.pics_big)
					
				})
			},
			onClick(e){
				console.log(e)
				if(e.index === 1 ){
					uni.switchTab({
						url:'/pages/cart/cart'
					}) 
				}
			}
		}
	}
</script>

<style lang="scss">
swiper{
	height: 750rpx;
	
	image{
		height: 100%;
		width: 100%;
	}
}
.goods-info-box{
	padding: 10px;
	padding-right: 0;
	.price{
		color: #C00000;
		font-size: 18px;
		margin: 10px 0;
	}
	.goods-info-body{
		display: flex;
		justify-content: space-between;
		.goods-name{
			font-size: 13px;
			margin-right: 10px;
		}
		.favi{
			width: 120px;
			font-size: 12px;
			display: flex;
			flex-direction: column;
			align-items:center;
			justify-context:center;
			border-left: 1px solid #efefef;
			color: gray;
		}
	}
	.yf{
		font-size: 12px;
		color: gray;
		margin: 10px 0;
	}
}
.goods-detail-container {
  // 给页面外层的容器，添加 50px 的内padding，
  // 防止页面内容被底部的商品导航组件遮盖
  padding-bottom: 50px;
}

.goods_nav {
  // 为商品导航组件添加固定定位
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
}
</style>
