<template>
	<div class="ctn">
		<div class="loading" v-if="loading">
			<div class="spinner">
				<div class="bounce1"></div>
				<div class="bounce2"></div>
				<div class="bounce3"></div>
			</div>
		</div>
		<div v-if="getdone">
			<div v-if="!!data.image" class="top">
				<div class="top-img">
					<img :src="data.image" :alt="data.image_source">
					<div class="slide-content">
						<p>{{data.title}}</p>
					</div>
				</div>
			</div>
			<div class="article" v-html="data.body"></div>
		</div>
	</div>
</template>

<script>
	import api from "../api/index.js"

	export default {
		data() {
			return {
				loading: false,
				getdone: false,
				data: {}
			}
		},
		created() {
			this.getContent()
		},
		watch: {
			"$route": "getContent"
		},
		methods: {
			getContent() {
				var that = this
				this.loading = true
				this.getdone = false
				var cid = this.$route.params.id
				api.getMessage('newsDetail', cid).then((data) => {
					that.data = data.data
					this.loading = false
					this.getdone = true
				}).catch(err => {
					console.log(':', err);
				})
			},
			backroute() {
				this.$router.go(-1)
			}
		}
	}
</script>

<style lang="scss">

	.ctn {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0px;
		overflow-y: scroll;
		overflow-x: hidden;
		-webkit-overflow-scrolling: touch;
		
		.loading {
			position: absolute;
			width: 100%;
			font-size: 34px;
			line-height: 55px;
			padding-top: 200px;
			color:#999;
		}

	}



	.article {
		position: relative;
		padding: 4px 26px;
		margin-bottom: 67px;
		text-align: left;
		font-size: 26px;
		line-height: 44px;
		word-break:break-all;
		font-family:Helvetica, 'Hiragino Sans GB', 'Microsoft Yahei', '微软雅黑', Arial, sans-serif;

		a, a:hover, a:visited, a:link, a:active {
				text-decoration: none;
				color: #4786b3;
				-webkit-tap-highlight-color:rgba(0,0,0,0);
		}

		.headline{
			margin:30px 0 10px;
			padding: 0 16px;
		}

		h1 {
			font-size: 40px;
			line-height: 50px;
		}

		h2 {
			font-size: 33px;
			line-height: 45px;
		}
		
		.meta {
			display: inline-block;
			height: 38px;
			line-height: 38px;
			// margin-bottom: 26px;
			width: 95%;
			overflow: hidden;
			text-overflow: ellipsis;
			white-space: nowrap;
			// float: left;

			img {
				vertical-align: middle;
				float: left;
			}
			.author {
				font-weight: bold;
				margin-left: 8px;
			}
			
		}
		.content {
			clear: both;
			// margin-top: 23px;

			a, a:hover, a:visited, a:link, a:active {
				text-decoration: none;
				color: #4786b3;
				-webkit-tap-highlight-color:rgba(0,0,0,0);
			}

			p {
				margin: 22px 0;
				// font-size: 16px;

			}
			iframe {
				width: 560px;
    			height: 435.75px;
			}
			img {
				width: 100%;
			}
		}
		.view-more {
			background-color: #ddd;
			text-align: center;
			height: 56px;
			line-height: 52px;
			margin: 19px 0 30px;

			a {
				display: block;
				font-size: 27px;
				width: 100%;
				height: 100%;
				text-decoration: none;
				color: #4786b3;
			}
		}
		blockquote{
			border-left:3px solid #D0E5F2;
			padding-left:15px;
		}
	}
	.question {
		overflow:hidden;
    padding: 0 12px;
	}
	.question + .question {
    border-top: 5px solid #f5f5f5;
	}
	.answer + .answer {
    padding-top: 50px;
		border-top: 3px solid #f5f5f5;
	}
</style>