<template>
	<view>
		<!-- 顶部选项卡 -->
		<scroll-view scroll-x class="border-bottom scroll-row" style="height: 80rpx;" :scroll-into-view="scrollinto"
		 :scroll-with-animation="true">
			<view class="scroll-row-item px-3" :class="tabIndex === index ? 'main-text-color':''" style="height: 80rpx;line-height: 80rpx;"
			 v-for="(item,index) in tabBars" :key="index" @tap="changeTab(index)" :id="'tab'+index">
				<text class="font-md">{{item.name}}</text>
			</view>
		</scroll-view>
		<swiper :current="tabIndex" :style="'height:'+scrollH +'px;'" @change="onChangeTab">
			<swiper-item v-for="(item,index) in newitems" :key="index">
				<scroll-view scroll-y="true" :style="'height:'+scrollH +'px;'" @scrolltolower="loadmore(index)">
					<block v-for="(list,listIndex) in item.list" :key="listIndex">

						<!-- 轮播图组件 -->
						<swiper-image v-if="list.type==='swiper'" :resdata="list.data" />

						<template v-if="list.type==='indexnavs'">
							<!-- 首页分类 -->
							<index-nav :resdata="list.data" />
							<divider />
						</template>


						<template v-if="list.type==='threeAdv'">
							<!-- 三图广告 -->
							<three-adv :resdata="list.data" />
							<divider />
						</template>


						<!-- 大图广告位 -->
						<!-- <card headTitle="每日精选" bodyCover="/static/images/demo/demo4.jpg" /> -->
						<!-- 公共列表组件 -->
						<view class="row j-sb" v-if="list.type==='commonList'">
							<block v-for="(item2,index2) in list.data" :key="index2">
								<common-list :item="item2" :index="index2"></common-list>
							</block>
						</view>
					</block>
					<!-- 上拉加载更多 -->
					<view class="d-flex a-center j-center text-light-muted font-md py-3">
						{{item.loadtext}}
					</view>
				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	// 模拟后端数据
	let demoT = [{
			name: "关注"
		},
		{
			name: "推荐"
		},
		{
			name: "体育"
		},
		{
			name: "热点"
		},
		{
			name: "财经"
		},
		{
			name: "娱乐"
		},
		{
			name: "军事"
		},
		{
			name: "历史"
		},
		{
			name: "本地"
		}
	];

	let demoB = [{
			type: "swiper",
			data: [{
					src: "../../static/images/demo/demo4.jpg"
				},
				{
					src: "../../static/images/demo/demo4.jpg"
				},
				{
					src: "../../static/images/demo/demo4.jpg"
				},
				{
					src: "../../static/images/demo/demo4.jpg"
				}
			]
		},
		{
			type: "indexnavs",
			data: [{
					src: "../../static/images/indexnav/1.png",
					text: "新品发布"
				},
				{
					src: "../../static/images/indexnav/2.gif",
					text: "小米众筹"
				},
				{
					src: "../../static/images/indexnav/3.gif",
					text: "以旧换新"
				},
				{
					src: "../../static/images/indexnav/4.gif",
					text: "1分拼团"
				},
				{
					src: "../../static/images/indexnav/5.gif",
					text: "超值特卖"
				},
				{
					src: "../../static/images/indexnav/6.gif",
					text: "小米秒杀"
				},
				{
					src: "../../static/images/indexnav/7.gif",
					text: "真心想要"
				},
				{
					src: "../../static/images/indexnav/8.gif",
					text: "电视热卖"
				},
				{
					src: "../../static/images/indexnav/9.gif",
					text: "家电热卖"
				},
				{
					src: "../../static/images/indexnav/10.gif",
					text: "米粉卡"
				}
			]
		},
		{
			type: "threeAdv",
			data: {
				big: {
					src: "../../static/images/demo/demo1.jpg"
				},
				smalltop: {
					src: "../../static/images/demo/demo2.jpg"
				},
				smallbottom: {
					src: "../../static/images/demo/demo3.jpg"
				}
			}
		},
		{
			type: "commonList",
			data: [{
					cover: "../../static/images/demo/list/1.jpg",
					title: "米家空调",
					desc: "1.5匹变频",
					oprice: "2699",
					pprice: 1399
				},
				{
					cover: "../../static/images/demo/list/1.jpg",
					title: "米家空调",
					desc: "1.5匹变频",
					oprice: "2699",
					pprice: 1399
				},
				{
					cover: "../../static/images/demo/list/1.jpg",
					title: "米家空调",
					desc: "1.5匹变频",
					oprice: "2699",
					pprice: 1399
				},
				{
					cover: "../../static/images/demo/list/1.jpg",
					title: "米家空调",
					desc: "1.5匹变频",
					oprice: "2699",
					pprice: 1399
				}
			]
		}

	];
	import swiperImage from '@/components/index/swiper-image.vue';
	import indexNav from '@/components/index/index-nav.vue'
	import threeAdv from '@/components/index/three-adv.vue'
	import card from '@/components/common/card.vue'
	import commonList from '@/components/common/common-list.vue'
	export default {
		data() {
			return {
				scrollinto: "",
				scrollH: 500,
				tabIndex: 0,
				tabBars: [],
				newitems: [],
			}
		},
		onLoad() {
			// 获取可视区域高度
			uni.getSystemInfo({
				success: (res) => {
					this.scrollH = res.windowHeight - uni.upx2px(82)
				}
			})
			this.__init();
		},
		methods: {
			// 初始化事件
			__init(){
				// 获取顶部选项卡
				this.tabBars = demoT;
				// 根据顶部选项卡生成页面
				let arr = []
				for (let i = 0; i < this.tabBars.length; i++) {
					let obj = {
						list:[],
						// 1. 上拉加载更多 2. 加载中... 3. 没有更多了
						loadtext:"上拉加载更多"
					}
					// 获取首屏数据
					if(i === 0){
						obj.list = demoB
					}
					arr.push(obj)
				}
				this.newitems = arr;
			},
			// 切换选项卡
			changeTab(index) {
				if (this.tabIndex === index) {
					return;
				}
				this.tabIndex = index
				this.scrollinto = 'tab' + index;
				// 加载数据
				this.addData();
			},
			// 监听滑动列表
			onChangeTab(e) {
				this.changeTab(e.detail.current)
			},
			addData(){
				// 获取当前索引
				let index = this.tabIndex;
				// 请求数据库
				this.newitems[index].list = demoB
			},
			// 上拉加载更多
			loadmore(index){
				let item = this.newitems[index];
				if(item.loadtext  !== '上拉加载更多') return;
				item.loadtext = '加载中...';
				setTimeout(()=>{
					item.list = [...item.list,...demoB]
					item.loadtext = '上拉加载更多'
				},2000)
				
			}
		},
		components: {
			swiperImage,
			indexNav,
			threeAdv,
			card,
			commonList
		}
	}
</script>

<style>
</style>
