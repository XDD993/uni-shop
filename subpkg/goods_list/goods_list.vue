<template>
	<view>
		goods
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		},
		onLoad(options) {
			const title = options.query
			console.log(options.query)
			if(title){
				wx.setNavigationBarTitle({
					 title
				})
			}
		}
	}
</script>

<style lang="scss">

</style>
