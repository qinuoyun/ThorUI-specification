<template>
	<scroll-view scroll-y class="tui-popup-scroll">
		<view class="tui-scrollview-box">
			<!-- tui-attr-active -->
			<block v-for="(Item,n) in specifications" :key="n">
				<view class="tui-bold tui-attr-title">{{Item.name}}</view>
				<view class="tui-attr-box">
					<view class="tui-attr-item" v-for="(oItem,index) in Item.item" :key="index" v-on:click="specificationBtn(oItem.name,n,$event,index)"
					 v-bind:class="[(oItem.isShow || oItem.isShow == undefined)?'':'tui-attr-none-active',subIndex[n] == index?'tui-attr-active':'']">
						{{oItem.name}} {{oItem.isShow}}
					</view>
				</view>
			</block>
		</view>
	</scroll-view>
</template>
<script>
	export default {
		props: {
			simulated: [Array, Object],
			value: [Array, Object],
		},
		data() {
			return {
				difference: [],
				specifications: [],
				selectArr: [], //存放被选中的值
				shopItemInfo: {}, //存放要和选中的值进行匹配的数据
				subIndex: [], //是否选中 因为不确定是多规格还是但规格，所以这里定义数组来判断
			}
		},
		created: function() {
			var self = this;

			self.difference = self.simulated.difference;
			self.specifications = self.simulated.specifications;

			for (var i in self.difference) {
				//修改数据结构格式，改成键值对的方式，以方便和选中之后的值进行匹配
				self.shopItemInfo[self.difference[i].difference] = self.difference[
					i];
			}
			self.checkItem();
		},
		methods: {
			shopItemInfoBoot() {
				var self = this;
				for (var i in self.difference) {
					//修改数据结构格式，改成键值对的方式，以方便和选中之后的值进行匹配
					self.shopItemInfo[self.difference[i].difference] = self.difference[
						i];
				}
			},
			specificationBtn: function(item, n, event, index) {
				var self = this;
				if (self.selectArr[n] != item) {
					self.selectArr[n] = item;
					self.subIndex[n] = index;

				} else {
					self.selectArr[n] = "";
					self.subIndex[n] = -1; //去掉选中的颜色 
				}
				self.checkItem();
			},
			checkItem: function() {
				var self = this;
				var option = self.specifications;

				var result = []; //定义数组存储被选中的值
				for (var i in option) {
					result[i] = self.selectArr[i] ? self.selectArr[i] : '';
				}
				for (var i in option) {
					var last = result[i]; //把选中的值存放到字符串last去
					for (var k in option[i].item) {
						result[i] = option[i].item[k].name; //赋值，存在直接覆盖，不存在往里面添加name值
						option[i].item[k].isShow = self.isMay(result); //在数据里面添加字段isShow来判断是否可以选择
					}
					result[i] = last; //还原，目的是记录点下去那个值，避免下一次执行循环时避免被覆盖
				}
				self.$forceUpdate(); //重绘
				this.$emit('input', this.selectArr);
			},
			isMay: function(result) {
				for (var i in result) {
					if (result[i] == '') {
						return true; //如果数组里有为空的值，那直接返回true
					}
				}
				return this.shopItemInfo[result].stock == 0 ? false : true; //匹配选中的数据的库存，若不为空返回true反之返回false
			}

		}
	}
</script>

<style>
	.tui-popup-scroll {
		height: 600upx;
		font-size: 26upx;
	}

	.tui-scrollview-box {
		padding: 0 30upx 60upx 30upx;
		box-sizing: border-box;
	}

	.tui-attr-title {
		padding: 10upx 0;
		color: #333;
	}

	.tui-attr-box {
		font-size: 0;
		padding: 20upx 0;
	}

	.tui-attr-item {
		max-width: 100%;
		min-width: 200upx;
		height: 64upx;
		display: -webkit-inline-flex;
		display: inline-flex;
		align-items: center;
		justify-content: center;
		background: #f7f7f7;
		padding: 0 26upx;
		box-sizing: border-box;
		border-radius: 32upx;
		margin-right: 20upx;
		margin-bottom: 20upx;
		font-size: 26upx;
	}

	.tui-attr-none-active {
		background-color: #ccc;
		opacity: 0.4;
		color: #000;
		pointer-events: none;
	}

	.tui-attr-active {
		background: #fcedea !important;
		color: #e41f19;
		font-weight: bold;
		position: relative;
	}

	.tui-attr-active::after {
		content: "";
		position: absolute;
		border: 1upx solid #e41f19;
		width: 100%;
		height: 100%;
		border-radius: 40upx;
		left: 0;
		top: 0;
	}

	.tui-number-box {
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 20upx 0 30upx 0;
		box-sizing: border-box;
	}
</style>
