<template>
<div id="app">
	<header class="header">
		<!--<i v-if="!flag" class="head-icon" @click="toggle(true)">-->
			<!--<icon slot="icon" name="snowMan" scale="4"></icon>-->
		<!--</i>-->
		<div class="search">
			<search v-if="!flag"
					@result-click="resultClick"
					@on-change="getResult"
					:results="results"
					v-model="value"
					position="absolute"
					@on-focus="onFocus"
					@on-cancel="onCancel"
					@on-submit="onSubmit"
					ref="search"></search>
		</div>

		<i v-if="flag" class="iconfont icon-ic_back" @click="back()"></i>
	</header>
	<aside class="aside" :class="{open:open,docked:docked}" @click="toggle()">
		<ul>
			<div class="user-info" @click="change(7)">
				<div class="head"><icon slot="icon" name="snowMan" scale="10"></icon></div>
				<div class="info">HYH</div>
			</div>
			<li :class="{chose:num == 1}" @click="change(1)">
				<span>首页</span>
				<i class="iconfont" :class="{'iconcolor icon-ic_star_black':num == 1,'icon-ic_star':num != 1}" />
			</li>
			<li :class="{chose:num == 2}" @click="change(2)">
				<span>我的衣柜</span>
				<i class="iconfont" :class="{'iconcolor icon-ic_star_black':num == 2,'icon-ic_star':num != 2}" />
			</li>
			<li :class="{chose:num == 3}" @click="change(3)">
				<span>发现</span>
				<i class="iconfont" :class="{'iconcolor icon-ic_star_black':num == 3,'icon-ic_star':num != 3}" />
			</li>
			<li :class="{chose:num == 4}" @click="change(4)">
				<span>潮流穿搭</span>
				<i class="iconfont" :class="{'iconcolor icon-ic_star_black':num == 4,'icon-ic_star':num != 4}" />
			</li>
			<li :class="{chose:num == 5}" @click="change(5)">
				<span>我的装扮</span>
				<i class="iconfont" :class="{'iconcolor icon-ic_star_black':num == 5,'icon-ic_star':num != 5}" />
			</li>
			<li :class="{chose:num == 6}" @click="change(6)">
				<span>设置</span>
				<i class="iconfont" :class="{'iconcolor icon-ic_star_black':num == 6,'icon-ic_star':num != 6}" />
			</li>
			<!-- <li :class="{chose:num == x.id}" v-for="(x, index) in list" @click="change(x.id)">
				<span>{{x.name}}</span>
				<i class="iconfont " :class="{'iconcolor icon-ic_star_black':num == x.id,'icon-ic_star':num != x.id}" />
			</li> -->
		</ul>
		<div class="cover" @touchmove="prevent"></div>
	</aside>
	<div v-if="circle" class="circle" @click="top()">
		<i class="iconfont icon-ic_top"></i>
	</div>
	<transition :name="transitionName">
		<keep-alive>
			<router-view class="app-view" :class="{'app-view-hidden':docked}"></router-view>
		</keep-alive>
	</transition>


	<tabbar style="z-index: 10 !important;">
		<tabbar-item link="/home">
			<icon slot="icon" name="sun" scale="20" spin></icon>
			<!--<span slot="label">首页</span>-->
		</tabbar-item>
		<tabbar-item link="/article">
			<icon slot="icon" name="chameleon" scale="20" class="animation"></icon>
		</tabbar-item>
		<!--<tabbar-item link="theme">-->
			<!--<icon slot="icon" name="pie" scale="20" class="animation"></icon>-->
			<!--<span slot="label">Explore</span>-->
		<!--</tabbar-item>-->
		<!--<tabbar-item link="theme">-->
			<!--<icon slot="icon" name="pie" scale="20" class="animation"></icon>-->
			<!--<span slot="label">Explore</span>-->
		<!--</tabbar-item>-->
		<tabbar-item badge="2" link="/userIndex">
			<icon slot="icon" name="snowMan" scale="4"></icon>
			<!--<span slot="label">个人主页</span>-->
		</tabbar-item>
	</tabbar>
</div>
</template>

<script>
	import {
		mapState
	} from 'vuex'
    import { Tabbar, TabbarItem, Group, Cell, Search} from 'vux'
	import extraMenu from './components/extraMenu'
	import api from './api/index'
	export default {
		computed: mapState({
			circle: state => state.circleFlag,
			num: state => state.num,
			flag: state => state.drawer
		}),
		mounted: function() {
			let vue = this;
			api.getTopics().then(function(data) {
				vue.list = data.data.others;
			});
		},
		data() {
			return {
				list: [],
				timer:'',
				open: false,
				docked: false,
				transitionName: 'slide-left',
				results:[
				    {title:'hello',otherData:'hyh'},
					{title:'what',otherData:'huya'},
                    {title:'hello',otherData:'hyh'},
                    {title:'what',otherData:'huya'},
                    {title:'hello',otherData:'hyh'},
                    {title:'what',otherData:'huya'},
                    {title:'hello',otherData:'hyh'},
                    {title:'what',otherData:'huya'},

				],
                value:''
			}
		},
        components: {
            Tabbar,
            TabbarItem,
            Group,
            Cell,
            Search,
            extraMenu
        },
		watch: {
			'$route' (to, from) {
				let vue = this;
				this.timer = setTimeout(function() {
					if(vue.open){
						vue.open = false;
						vue.docked = false;
					}
					clearTimeout(vue.timer);
				}, 300);
				to.path == '/' && this.num != 1 && this.$store.commit('add', 1);
				this.transitionName = to.path != "/article" ? 'slide-right' : 'slide-left';
			}
		},
		methods: {
			back(n) {
				if (n) {
					this.$router.push({
						path: 'home'
					});
				} else {
					window.history.back()
				}
			},
			toggle() {
				if (!this.open) {
					this.docked = true;
					this.open = true;
				} else {
					this.open = false;
					let vue = this;
					setTimeout(function() {
						vue.docked = false;
					}, 300);
				}
			},
			change(id) {
				let path = id == 1 ? 'home' : 'theme';
				if(id == 7)
				    path = 'user';
				this.$router.push({
					path: path,
					query: {
						id: id || ""
					}
				});

				this.$store.commit('add', id);
			},
			prevent(event) {
				event.preventDefault()
				event.stopPropagation()
			},
			jump() {
				window.open("https://github.com/walleeeee/daily-zhihu");
			},
			top() {
				let vue = this;
				let dom = document.querySelector('.app-view');
				let height = dom.scrollTop;
				let scrollTop = parseInt(height / 50);
				let time = setInterval(function() {
					height -= scrollTop;
					if (height <= 0) {
						dom.scrollTop = 0;
						vue.$store.commit('toggle');
						clearInterval(time);
					} else {
						dom.scrollTop = height;
					}
				}, 1);
			},
			setFocus () {
                this.$refs.search.setFocus()
            },
            resultClick (item) {
                window.alert('you click the result item: ' + JSON.stringify(item))
            },
            getResult (val) {
                console.log('on-change', val)
                //this.results = val ? getResult(this.value) : []
            },
            onSubmit () {
                this.$refs.search.setBlur()
                this.$vux.toast.show({
                    type: 'text',
                    position: 'top',
                    text: 'on submit'
                })
            },
            onFocus () {
                console.log('on focus')
            },
            onCancel () {
                console.log('on cancel')
            }
		}
	}
</script>

<style lang="less">
	p{
		margin: 0;
	}
	.shadow-box{
		position: relative;

		margin: 10px 0px;
		padding: 10px;

		display: flex;

		background-color: #fff;

		border-width: 0.15rem;
		border-color: #ccc;
		border-radius: 5px;
		box-shadow: 0 3px 10px 0 rgba(91, 115, 146, 0.15);

		img{
			width: 2rem;
			height: 100%;
			margin-right: 0.4rem;

		}
		p{
			height: auto;
		}
	}
	.user-info{
		.head{
			text-align: center;
		}
		.info{
			text-align: center;
		}
	}
	.head-icon{
		position: relative;
		top: 10px;

	}
	.animation {
		animation: changeColor 5s infinite;
	}
	@keyframes changeColor {
		0% {
			color: red;
		}
		20% {
			color: yellow;
		}
		40% {
			color: blue;
		}
		60% {
			color: green;
		}
		80% {
			color: purple;
		}
		100% {
			color: gold;
		}
	}
	.slide-left-enter,
	.slide-right-leave-active {
		opacity: 0;
		-webkit-transform: translate(50vw, 0);
	}

	.slide-left-leave-active,
	.slide-right-enter {
		opacity: 0.1;
		-webkit-transform: translate(-50vw, 0);
	}

	.app-view {
		padding-bottom: 70px;
		z-index: 1;
		width: 100vw;
		height: 100vh;
		overflow: auto;
		position: absolute;
		transition: transform 0.3s ease;
		-webkit-overflow-scrolling: touch;
	}

	.app-view-hidden {
		overflow: hidden;
	}

	.header {
		width: 100%;
		z-index: 9;
		position: relative;
		//background-image: linear-gradient(0deg, rgba(0, 0, 0, 0.00) 0%, rgba(0, 0, 0, 0.51) 95%);
		background-color: #fff;
		.iconfont {
			color: #000;
			font-size: 0.8rem;

			position: absolute;
			z-index: 10;
			top: 7px;
			left: 10px;

		}
	}

	.aside {
		z-index: 11;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		position: fixed;
		visibility: hidden;
		&::-webkit-scrollbar {
			display: none!important;
			width: 0!important;
			height: 0!important;
			-webkit-appearance: none;
			opacity: 0!important;
		}
		ul {
			margin: 0;
			float: left;
			width: 60%;
			height: 100%;
			padding: 0.5rem 0.5rem 0.5rem;
			overflow: auto;
			//background: rgba(91, 116, 146, 0.75);
			background-color: #eee;
			transform: translate(-100%, 0);
			transition: transform 0.3s ease;
			-webkit-overflow-scrolling: touch;
			&::-webkit-scrollbar {
				display: none!important;
				width: 0!important;
				height: 0!important;
				-webkit-appearance: none;
				opacity: 0!important;
			}
		}
		li {
			cursor: pointer;
			font-size: 0.43rem;
			list-style: none;
			color: #aaa;
			margin-top: 20px;
			.iconfont {
				float: right;
				font-size: 0.6rem;
			}
			&.chose {
				color: #FFD300;
			}
		}
		.cover {
			width: 100%;
			height: 100%;
			opacity: 0;
			display: none;
			background: rgba(172, 185, 201, 0.40);
			transition: opacity 0.3s ease;
		}
		&.open {
			ul {
				transform: translate(0);
			}
			.cover {
				opacity: 1;
			}
		}
		&.docked {
			visibility: visible;
			.cover {
				display: block;
			}
		}
	}

	.circle {
		width: 40px;
		height: 40px;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.80);
		box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.50);
		right: 5%;
		bottom: 17vw;
		position: fixed;
		z-index: 10;
		i {
			top: 50%;
			left: 50%;
			font-size: 0.6rem;
			color: #ACB9C9;
			transform: translate(-50%, -50%);
			position: absolute;
		}
	}

	@media screen and (min-width: 640px) {
		.app-view {
			width: 640px;
			left: 50%;
			transform: translate(-50%, 0);
		}
		.aside ul {
			width: 350px;
		}
	}
</style>
