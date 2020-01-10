<template>
	<view class="bg">
		<text class="winNum">当前战绩： <text>总局数:{{gameSum}} 胜:{{winNum}} 败:{{lostNum}} 平:{{drawNum}}</text></text>
		<view class="showInfo">
			<image class="img_left" :src="img_left"></image>
			<text class="gameInfo">{{win_lost}}</text>
			<image class="img_right" :src="img_right"></image>

		</view>
		<view v-show="drawOff">
			<text style="font-size:35rpx;">抽奖环节：恭喜您获得 ———<text class="drawInfo">{{drawhj}}</text></text>


			<button style="margin-top:50rpx;" @click="starMove" :disabled="btnOff_draw">开始滚动</button>
			<button style="margin-top:20rpx;" @click="stopMove">停止滚动</button>
			<button style="margin-top:20rpx;" @click="gopage">返回页面</button>
		</view>
		<text class="str_Info">{{str_Info}}</text>
		<view class="user_choose" v-show="gameOff">
			<text class="winNum">请出拳</text>
			<view class="img_choose">
				<block v-for="(item,index) in img_srcs" :key="index">
					<view class="choose_obj" @click="getUserChange" :id="index">
						<image class="chooseImg" :src="item"></image>
					</view>
				</block>
			</view>
			<button class="againBtn" @click="again">再来一次</button>
		</view>
	</view>
</template>

<script>
	var num = 0,
		LuckNum = 0,
		timer, timer_two, otimerout;
	//分别定义用于出拳图片切换定时器数字，抽奖滚动定时器数字，出拳定时器，抽奖定时器，提示信息定时器
	export default {
		data() {
			return {
				btnOff: false, //用于选择出拳开关，防止重复点击
				gameSum: 0, //总局数
				drawNum: 0, //平局次数
				lostNum: 0, //输次数
				winNum: 0, //赢次数
				gameOff: true, //用于控制选择出拳模块的显示隐藏
				drawOff: false, //用于控制抽奖模块的显示隐藏
				win_lost: '', //出拳结果提示信息
				img_left: '', //左边人机出拳图片
				str_Info: '说明:10局为一回合(胜局+败局=10;平局不算)，胜局数达到6局以上(包含6局)，进入抽奖环节', //说明			
				btnOff_draw: false, //抽奖模块中的开始滚动开关，防止重复点击		
				img_right: '../../static/wenhao.png', //右边用户选择的出拳图片
				drawhj: '请开始抽奖', //抽奖模块提示信息
				luckDraw_arr: [ //奖品数组
					'奖品1',
					'奖品2',
					'奖品3',
					'奖品4',
					'奖品5',
					'奖品6',
					'奖品7',
					'奖品8',
					'奖品9',
					'奖品10',
					'奖品11',
					'奖品12',
					'奖品13',
					'奖品14',
					'奖品15'
				],
				img_srcs: [ //出拳图片数组
					'../../static/shitou.png',
					'../../static/jiandao.png',
					'../../static/bu.png'
				]

			}
		},
		onLoad() {
			//定时器开始
			this.timerstar();
		},
		methods: {

			//定时器开始，图片开始跳动
			timerstar() {
				timer = setInterval(this.randomNum, 80)
			},
			randomNum() {
				//获取随机数
				if (num >= this.img_srcs.length) {
					num = 0;
				}
				//随机获取数组中电脑石头剪刀布相应的图片。
				this.img_left = this.img_srcs[num],
					num++

			},
			//点击获取用户选择
			getUserChange(e) {
				if (this.lostNum + this.winNum <= 9) { //只有胜局和败局的和小于等于9才可以选择出拳，意为10局一回合，每10局重置（平局不算）
					var randomNum = Math.floor(Math.random() * 3); //产生一个随机数
					if (this.btnOff == true) { //如果用户已经选择了出拳，就不能重复点击，除非点击再来一次，将btnoff状态改为false
						return;
					}

					this.img_right = this.img_srcs[e.currentTarget.id] //根据用户点击得图片得id将右边得图片变为用户选择出拳的图片
					clearInterval(timer); //停止图片滚动定时器

					this.img_left = this.img_srcs[randomNum]; //根据随机数随机选择左边电脑人机得出拳图片
					this.gameSum++; //游戏次数(总局数)加1

					this.compare() //调用判断用户输赢事件					
					this.btnOff = true; //用于出拳后，不能重复点击

					if (this.lostNum + this.winNum == 10) { //当 胜局与败局得和等于10,意为回合结束
						if (this.winNum >= 6) { //判断用户的胜局是否为6局以上
							this.drawOff = true; //抽奖模块显示
							this.gameOff = false; //选择出拳模块隐藏
						} else { //如果胜局未到6局
							this.gameSum = 0; //游戏次数重置
							this.str_Info = "回合结束，胜局未达到要求，2秒后重置数据"; //提示信息
							this.btnOff = false; //胜局达到10，恢复选择出拳的可点击状态
							otimerout = setTimeout(this.timerout, 2500); //2.5秒后返回游戏页面

						}
					}
				}

			},
			compare() { //判定输赢事件		
				//我是根据图片路径判断输赢的
				var user = this.img_right; //定义右边用户的图片路径
				var renji = this.img_left; //定义左边人机的图片路径
				if (user == '../../static/shitou.png' && renji == '../../static/jiandao.png') {
					this.winNum++; //胜利次数加一
					this.win_lost = '恭喜！您赢了'; //提示信息

				} else if (user == '../../static/jiandao.png' && renji == '../../static/bu.png') {
					this.winNum++; //胜利次数加一
					this.win_lost = '恭喜！您赢了'; //提示信息

				} else if (user == '../../static/bu.png' && renji == '../../static/shitou.png') {
					this.winNum++; //胜利次数加一
					this.win_lost = '恭喜！您赢了'; //提示信息

				} else if (user == renji) {
					this.win_lost = '平局'; //提示信息
					this.drawNum++; //平局次数加一
				} else {
					this.lostNum++; //输次数加一
					this.win_lost = '很抱歉，您输了！'; //提示信息
				}
			},
			//抽奖滚动开始

			starMove() {
				this.timer_twostar() //定时器开始，抽奖开始滚动
				//防止按钮开始滚动重复点击
				this.btnOff_draw = true;
			},
			//抽奖滚动停止
			stopMove() {
				clearInterval(timer_two) //关闭定时器，停止抽奖滚动
			},
			//返回页面
			gopage() {
				this.timerout() //执行返回游戏页面并重置数据事件
			},
			//重置页面，让页面恢复初始的状态
			timerout() {
				this.timerstar();
				this.str_Info = '说明:10局为一回合(胜局+败局=10;平局不算)，胜局数达到6局以上(包含6局)，进入抽奖环节';
				this.drawhj = '请开始抽奖';
				this.btnOff = this.btnOff_draw = this.drawOff = false;
				this.gameOff = true;
				this.gameSum = this.drawNum = this.lostNum = this.winNum = 0; //总局数，平局数，输次数赢次数全都重置为0
			},
			//抽奖定时器
			timer_twostar() {
				timer_two = setInterval(this.luckDraw, 10)
			},
			//抽奖环节滚动事件
			luckDraw() {
				//获取随机数
				if (LuckNum >= this.luckDraw_arr.length) {
					LuckNum = 0;
				}
				this.drawhj = this.luckDraw_arr[LuckNum]
				LuckNum++;

			},
			again() { //点击再来一次执行
				//按钮状态
				if (this.btnOff == false) {
					return;
				}
				//页面图片切换定时器开始
				this.timerstar();

				//恢复页面初始状态
				this.btnOff = false;
				this.win_lost = '';
				this.img_right = '../../static/wenhao.png';
			}
		}
	}
</script>

<style>
	uni-page-body, uni-page-refresh{
		height: 100%;
	}
	page{
		width: 100%;
		height: 100%;
		background: url(../../static/bg.jpg);
		background-size:cover ;
		background-repeat:no-repeat ;
	}
	.bg {
		width: 100%;
		height: 100%;
		
		text-align: center;
	}

	.winNum {
		padding-top: 40rpx;
		display: block;
		font-size: 30rpx;
		font-weight: bold;
	}

	.showInfo {
		display: flex;
		width: 100%;
		margin-top: 30rpx;
		height: 200rpx;
	}

	.img_left {
		height: 180rpx;
		width: 180rpx;
		margin-left: 80rpx;
	}

	.img_right {
		height: 180rpx;
		width: 180rpx;
		margin-right: 80rpx;
	}

	.drawInfo {
		font-size: 40rpx;
		color: orangered;
		flex: 1;
	}

	.gameInfo {
		color: orangered;
		flex: 1;
		font-size: 30rpx;
		margin-top: 75rpx;
	}

	.str_Info {
		display: block;
		padding: 25rpx 50rpx;
		font-size: 25rpx;
	}

	.user_choose {
		margin: 40rpx;
		height: 500rpx;
		background: rgba(245,235,144,0.5);
		text-align: center;
	}

	.img_choose {
		display: flex;
		margin-top: 40rpx;
	}

	.choose_obj {
		flex: 1;
		height: 120rpx;
		width: 100rpx;

	}

	.chooseImg {
		height: 120rpx;
		width: 120rpx;
	}

	.againBtn {
		font-weight: bold;
		margin: 80rpx;
		background: linear-gradient(120deg, #facd35 0%, rgb(241, 241, 57) 50%, #f5eb90 100%);
	}
</style>
