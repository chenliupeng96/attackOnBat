<template>
	<view>
		<view class="row j-center m-2">
			<block 
			v-for="(item, index) in resdata" 
			:key="index"
			>
				<view 
				class="span-4 d-flex flex-column font-sm j-center a-center py-1" 
				@tap="event(item)" 
				>
					<image :src="item.src" 
					mode="widthFix"
					style="width: 60rpx; height: 60rpx;"
					></image>
					<text>{{item.text}}</text>
				</view>
			</block>
			
		</view>
	</view>
</template>

<script>
	export default {
		props:{
			resdata:Array
		},
		methods:{
			event(item){
				console.log(item);
			}
		}
	}
</script>

<style>
</style>
