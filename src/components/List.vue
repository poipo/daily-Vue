<template>
	<div class="list">
		<div class="loading" v-if="loading">
			<div class="spinner">
				<div class="bounce1"></div>
				<div class="bounce2"></div>
				<div class="bounce3"></div>
			</div>
		</div>
		<div class="list-con">
			<div v-if="getdone" v-for="data in datas">
				<div class="top-slide top" v-if="!!data.top_stories">
					<swiper :options="swiperOption" class="sdj">
						<swiper-slide class="top-img" v-for="slide in data.top_stories" :key="slide" :data-id="slide.id">
							<div @click="toContent(slide.id)">
								<img :src="slide.image" :alt="slide.title">
								<div class="slide-content">
									<p>{{slide.title}}</p>
								</div>
							</div>
							
						</swiper-slide>
						<div class="swiper-pagination" slot="pagination"></div>
					</swiper>
				</div>
				<div class="list-message" v-if="!!data.stories">
					<p >{{data.top_stories?'今日新闻':data.date}}</p>
					<img v-if="!data.date && !!data.image" :src='"http://read.html5.qq.com/image?src=forum&q=5&r=0&imgflag=7&imageUrl=" + data.image'  :alt="data.name">
					<ul>
						<li v-for="list in data.stories" @click="toContent(list.id)" :data-id="list.id">
							<p>{{list.title}}</p>
							<img v-if="!!list.images" :src="list.images" :alt="list.title">
						</li>
					</ul>
					
				</div>
			</div>
				

		</div>

		<div v-if="getdone" class="more-load" :class="{dbload: homepage}">
			<div class="spinner">
				<div class="bounce1"></div>
				<div class="bounce2"></div>
				<div class="bounce3"></div>
			</div>
		</div>
		
	</div>
</template>

<script>
	import api from "../api/index.js"
	import { mapState } from "vuex"

	export default {
		data() {
			return {
				topStories: [1, 2, 3, 4],
				datas: [],
	        	date: 0,
	        	routetimes: false,
	        	homepage: false,
	        	refresh: true,
				loading: false,
				getdone: false,
				swiperOption: {
					autoplay: 3500,
					setWrapperSize :true,
					pagination : '.swiper-pagination',
					paginationClickable :true,
					mousewheelControl : true,
					observeParents:true,
					loop: true,
				}
			}
		},
		created() {
			// if(this.$route.path)
			// if(!this.$route.params.id) {}
			this.getlist();
		},
		mounted() {
			var rid = this.$route.path.split('\/')[2]
			this.routetimes = true
			if(!rid || rid === '0') {
				this.homepage = true
				this.$store.commit('setTitleName', '首页')
				document.querySelector('.list').addEventListener('scroll',this.lazyLoad);
			} else {
				this.homepage = false

			}
			
		},
		watch: {
			"$route": "getlist"
		},
		methods: {
			toContent(id) {
				this.$router.push({
	        		name: "Content",
	        		params: {
	        			id: id
	        		}
	        	})
			},
			getlist() {
				var rid = this.$route.path.split('\/')[2]
				if(this.routetimes) {
					if(!rid || rid === '0') {
						this.homepage = true
						document.querySelector('.list').addEventListener('scroll',this.lazyLoad);
					} else if(this.homepage) {
						this.homepage = false
						document.querySelector('.list').removeEventListener('scroll',this.lazyLoad);
					}
				}

				this.datas = [];
				this.date = 0;
				var that = this;
				var jumpId = this.$route.params.id
				var local = "newsThemeDetail"
				this.loading = true
				this.getdone = false
				// console.log(jumpId)
				// console.log(typeof jumpId)
				if(!jumpId || jumpId === '0') {
					// console.log("newsThemeDetail")
					jumpId = ""
					local = "news"
				}
				// console.log(local,': ', jumpId)
				api.getMessage(local, jumpId).then(function(data) {
					// console.log(data)
					var data = data.data;
					if(!!data.top_stories) {
						var top_stories = data.top_stories;
						var tlength = top_stories.length;
						for (var i = tlength - 1; i >= 0; i--) {
							// top_stories[i].image = 'http://read.html5.qq.com/image?src=forum&q=5&r=0&imgflag=7&imageUrl=' + top_stories[i].image
						}
						data.top_stories = top_stories
					}
					if(!!data.stories) {
						var stories = data.stories;
						var tlength = stories.length;
						for (var i = tlength - 1; i >= 0; i--) {
							if(!!stories[i].images) {
								// stories[i].images = 'http://read.html5.qq.com/image?src=forum&q=5&r=0&imgflag=7&imageUrl=' + stories[i].images
							}
						}
						data.stories = stories
					}

					if(!!data.date) {
						var date = data.date
						date = date.substring(0, 4) + "年" + date.substring(4, 6) + "月" + date.substring(6, 8) + "日"
						data.date = date
					}
						
					that.datas.push(data)
					that.loading = false
					that.getdone = true
					
					
				}).catch(err => {
					// console.log(err);
				});
				setInterval(() => {
					// console.log('simulate async data')
					// let swiperSlides = this.swiperSlides
					// if (swiperSlides.length < 10) swiperSlides.push(swiperSlides.length + 1)
				}, 3000)
			},
			lazyLoad(e) {
				var that = this,
					clientH = document.documentElement.clientHeight || document.body.clientHeight,
					scrollH = document.querySelector('.list').scrollTop,
					eleTop = document.querySelector('.more-load').offsetTop;

				if(eleTop - 100 <= (clientH + scrollH) && that.refresh) {
					that.refresh = false;
					var gd = that.getdate();
					// document.querySelector('.more-load').textContent = 'loading...'
					api.getMessage('newsDate', gd).then((data) => {
						that.datas.push(data.data)
						// document.querySelector('.more-load').textContent = 'load'
						that.date += 1
						that.refresh = true
					}).catch(err => {
						console.log(':', err);
					})
				}
			},
			getdate() {
				var dt = new Date();
				dt.setDate(dt.getDate() - this.date);
				var y = dt.getFullYear();
				var m = dt.getMonth() + 1;
				m = m >= 10 ? m : '0' + m;
				var d = dt.getDate();
				d = d >= 10 ? d : '0' + d;
				return '' + y + m + d;
			}

		}
	}

		
</script>

<style lang="scss">
	p {
		margin: 0;
	}
	
	.list:before{
		content:'';
		display:block;
		height:100px;
	}

	.list {
		position: absolute;
		top: 0;
		width: 100%;
		height: 100%;
		z-index: 1;
		overflow-y: auto;
		-webkit-overflow-scrolling: touch;
		background-color:#eee;

		.loading {
			position: absolute;
			// height: calc(100%-400px);
			width: 100%;
			font-size: 34px;
			padding-top: 100px;
		}

		.top-slide {
			// width: 100%;
			// height: 440px;
			margin-bottom:10px;
		}

		.list-message {
			font-size: 0;

			&>p {
				height: 40px;
				line-height: 80px;
				font-size: 20px;
				color: #999;
				// font-weight: bold;
				text-align:left;
				margin-left:30px;
			}

			&>img{
				margin-top:-40px;
			}

			ul {
				margin: 20px 20px ;
				padding: 0;
				background-color:#eee;

				li {
					display: flex;
					align-items: center;
					height: 156px;
					padding: 3px 0;
					margin-top:15px;
					background-color:#fff;
					padding:0 10px;
					border: {
						top: 1px solid #ddd;
					} 
					border-radius:6px;
					box-shadow:0px 2px 6px #ccc;

					p {
						flex: 1;
						text-align: justify;
						line-height: 36px;
						font-size: 1.6rem;
						padding: 0 18px 0 10px;
						margin-top:-15px;
						// font-weight: bold;
					}
					img {
						// flex: 75px;
						// width: 75px;
						width: 130px;
						overflow-y: hidden;
						// margin: 12.5px 0;
						align-items: center;
						// flex: ver
						// verticalCenter: middle;
						vertical-align: middle;
					}
				}
			}

		}
	}
	.more-load {
		display: none;
		line-height: 70px;
		font-size: 26px;
		color:#999;
		text-align: center;
		font-weight: bold;
		height: 70px;
		padding: 3px 0;
		margin-top:-100px;
	}
	.dbload {
		display: block;
	}
	.top {
		position: relative;
		height: 350px;
		.top-img {
			width: 100%;
			height: 350px;
			color: #000;
			background-color: #ddd;
			line-height: 440px;
			overflow: hidden;
			
			img {
				width: 100%;
				margin-top: -145px;
			}
		}
		.slide-content {
			position: absolute;
			width: 100%;
			height: 100%;
			top: 0;
			background-color: rgba(0, 0, 0, .3);

			p {
				position: absolute;
				font-size: 29px;
				// vertical-align: bottom;
				color: #fff;
				bottom: 23px;
				text-align: left;
				line-height: 44px;
				max-width: 560px;
				// left: 50%;
				margin-left: 30px;
			}
		}
	}
	.spinner {
		margin: 100px auto 0;
		width: 100px;
		text-align: center;
	}

	.spinner > div {
		width: 18px;
		height: 18px;
		background-color :#4786b3;

		border-radius: 100%;
		display: inline-block;
		-webkit-animation: sk-bouncedelay 1.4s infinite ease-in-out both;
		animation: sk-bouncedelay 1.4s infinite ease-in-out both;
	}

	.spinner .bounce1 {
		-webkit-animation-delay: -0.32s;
		animation-delay: -0.32s;
	}

	.spinner .bounce2 {
		-webkit-animation-delay: -0.16s;
		animation-delay: -0.16s;
	}

	@-webkit-keyframes sk-bouncedelay {
		0%, 80%, 100% { -webkit-transform: scale(0) }
		40% { -webkit-transform: scale(1.0) }
	}

	@keyframes sk-bouncedelay {
		0%, 80%, 100% { 
			-webkit-transform: scale(0);
			transform: scale(0);
		} 40% { 
			-webkit-transform: scale(1.0);
			transform: scale(1.0);
		}
	}
		
</style>