<template>
	<div class="nav" :class="{slide: nav}"  @click="navToggle">
		<div class="nav-content">
			<div class="nav-head">
				<div class="avatar"></div>
				<div class="user-name">{{ username }}</div>
				<div class="my-name" data-id="-1"><a href="https://github.com/poipo/daily-Vue">GitHub</a></div>
				<!--<div class="my-name">make by panzhiwei</div>-->
			</div>
			<ul class="nav-list">
				<li style="height:150px"></li>
				<li title="首页" data-id="0" @click="tolist(0, '首页')">首页</li>
				<li v-for="sub in subList" :title="sub.description" :data-id="sub.id" @click="tolist(sub.id, sub.name)"> {{ sub.name }} </li>
				<li v-for="list in othersList" :title="list.description" :data-id="list.id" @click="tolist(list.id, list.name)"> {{ list.name }} </li>
			</ul>
		</div>
	</div>
</template>

<script>
	import api from "../api/index.js"
	import { mapState } from "vuex"

	export default {
		data() {
			return {
				username: "知乎日报",
				subList: [],
				othersList: [],
				jumpId: ''
			}
		},
		computed: {
			...mapState({
	            nav: state => state.nav,
	        })
		},
		mounted() {
			var that = this;
			api.getMessage('newsThemes').then(function(data) {
				// console.log(data)
				that.subList = data.data.subscribed;
				that.othersList = data.data.others;
				var lid = that.$route.params.id;
				if(!lid) {
					lid = 0;
				}
				setTimeout(function () {
					if(!!document.querySelector('.nav-list li[data-id="' + lid + '"]')) {
						// console.log(lid)
						document.querySelector('.nav-list li[data-id="' + lid + '"]').classList.add('nav-choice')
					}
				}, 0)
				
			}).catch(err => {
				console.log(err);
			});
		},
		methods: {
	        navToggle() {
	            this.$store.commit('toggle', false)
	        },
	        choiceId(id, name) {
	        	if(!!document.querySelector('.nav-choice')) {
	        		document.querySelector('.nav-choice').classList.remove('nav-choice')
	        	}
	        	
	        	document.querySelector('.nav-list [data-id="' + id + '"]').classList.add('nav-choice')

	        	this.$store.commit('setLid', id)
	        },
	        tolist(id, cname) {
	        	if(id === 0) {
	        		this.$store.commit('setTitleName', '首页')
	        	} else {
	        		this.$store.commit('setTitleName', cname )
	        	}
	        	this.choiceId(id, cname)
        		this.$router.push({
	        		name: "ListId",
	        		params: {
	        			id: id
	        		}
	        	})
	        }
		}
	}
</script>

<style lang="scss" scoped>
	ul {
		list-style-type: none;
	}
	div.slide {
		left: 0;
		// animation: slided .5s ease-in-out forwards;
	}
	@keyframes slided {
		0% {
			left: -640px;
			// display: none;
		}
		2% {
			left: -420px;
		}
		100% {
			left: 0px;
			// display: block;
		}
	}
	.nav {
		position: absolute;
		width: 100%;
		height: 100%;
		line-height: 34px;
		z-index: 99;
		font-size: 24px;
		overflow: hidden;
		left: -640px;
		transition: left .6s;
		z-index: 99;
		// background-color: rgba(0, 0, 0, .5);

		.nav-mark {
			position: absolute;
			top: 0;
			left: 0;
			display: none;
			opacity: 0;
			width: 100%;
			height: 100%;
			background-color: rgba(0, 0, 0, .5);
		}

		.opmark {
			display: block;
			opacity: 1;
		}

		.nav-content {
			position: absolute;
			width: 420px;
			height: 100%;
			background-color: #fff;
		}
		
		.nav-head {
			height: 100px;
			width: 380px;
			float: left;
			line-height: 100px;
			padding: 20px 20px;
			background-color:#00a2ea;
			position:absolute;
			z-index:2;

			div {
				float: left;
			}
			.avatar {
				width: 100px;
				height: 100px;
				border-radius: 100px;
				background: #000 url('../assets/logo.jpg') left top no-repeat;
				background-size: contain;
			}
			.user-name {
				width: 280px;
				font-size: 30px;
				// padding-left: 24px;
				text-align: center;
				// font-weight: bold;
				color:#fff;
				// margin-top:-14px;
				margin-left:-20px;
			}
		}


		.nav-list {
			position: absolute;
			clear: both;
			width: 100%;
			height: 100%;
			padding: 0;
			// padding-top:128px;
			margin-top:-10px;
			line-height: 70px;
			text-align: left;
			overflow-y: scroll;

			li {
				padding-left: 24px;

				a, a:hover, a:visited, a:link, a:active {
				text-decoration: none;
				color: #4786b3;
				-webkit-tap-highlight-color:rgba(0,0,0,0);
				}	
			}

			.nav-choice {
				background-color: #eee;
				color: #666;
			}
			
			.github{
				text-decoration:none;
			}
		}
	}
	.my-name{
			position:absolute;
			right:20px;
			bottom:-30px;
			color:#fff;

			&>a{
				// text-decoration: none;
				color: #fff;
				-webkit-tap-highlight-color:rgba(0,0,0,0);
			}
	}
</style>