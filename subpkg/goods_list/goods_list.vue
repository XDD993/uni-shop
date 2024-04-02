<template>
	<view>
		<view class="goods-list">
			<view v-for="(item,i) in goodsList" :key="i" @click="gotoDetail(item)">
			<my-goods :goods="item"></my-goods>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				queryObj:{
					query:'',
					cid:'',
					pagenum:1,
					pagesize:10,
					// 商品列表的数据
				},
				goodsList:[],
				// 总条数
				total:0,
				defaultPic:'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png',
				isloading:false
			};
		},
		onLoad(options) {
			const title = options.query
			this.queryObj.query = options.query || ''
			this.queryObj.cid = options.cid || ''
			console.log(this.queryObj)
			// if(title){
			// 	wx.setNavigationBarTitle({
			// 		 title
			// 	})
			// }
			this.getGoodsList()
		},
		methods:{
			// 获取列表数据的方法
			async getGoodsList(cb){
				this.isloading = true
				const {data:res} = await uni.$http.get('/api/public/v1/goods/search',this.queryObj)
				this.isloading  = false
				// 只要数据请求完毕 就立刻按需调用cb回调函数
				cb && cb()
				if(res.meta.status!==200)
				return uni.$showMsg()
				// 新旧数据要进行拼接
				this.goodsList = [...this.goodsList,...res.message.goods]
				this.total = res.message.total
			},
			gotoDetail(item){
				uni.navigateTo({
					url:'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id
				})
			}
		},
		// 加载下一页的数据
		onReachBottom() {
			if(this.queryObj.pagenum * this.queryObj.pagesize >=this.total)
			return uni.$showMsg('到底啦')
			if(this.isloading)
			return
			this.queryObj.pagenum += 1
			this.getGoodsList()
		},
		// 下拉刷新
		onPullDownRefresh() {
			// 重置关键数据
			this.queryObj.pagenum = 1
			this.goodsList = []
			this.total = 0
			this.isloading = false
			this.getGoodsList(()=>uni.stopPullDownRefresh())
		}
	}
</script>
 
<style lang="scss">
.goods-item{
	display: flex;
	padding: 10px 5px;
	border-bottom: 1px solid #f0f0f0;
	.goods-item-left{
		margin-right: 5px;
		.goods-pic{
			width: 100px;
			height: 100px;
			display: block;
		}
		
	}
	.goods-item-right{
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		.goods-name{
			font-size: 13px;
			.goods-info-box{
				.goods-price{
					// color: #C00000;
					font-size: 26px;
				}
			}
		}
	}
}
</style>
