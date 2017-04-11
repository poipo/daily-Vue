<template>
	<div class="header">
		<div @click.stop="toggle" class="left-menu">
			<span></span>
		</div>
		<div class="title">{{ title }}</div>
	</div>
</template>

<script>
	import { mapState } from "vuex"

	export default {
		data() {
			return {
				// title: "今日热闻"
			}
		},
		computed: {
			...mapState({
				title: state => state.titleName,
			})
		},
		methods: {
			toggle() {
				this.$store.commit('toggle', true)
			}
		}
	}
</script>

<style scoped>
	.header {
		position: absolute;
		height: 100px;
		width: 100%;
		font: normal 1.7em/100px Serif;
		background-color: #00a2ea;
		z-index: 11;
	}
	.left-menu {
		position: absolute;
		width: 60px;
		height: 100px;
		margin-left:16px;
		-webkit-appearance: none;
		-webkit-tap-highlight-color:rgba(0,0,0,0);
	}
	.left-menu span{
		display: block;
		position: absolute;
		top: 50%;
		left: 50%;
		margin: -2px 0 0 -22px;
		height: 4px;
		width: 32px;
		background-color: #fff;
	}
	.left-menu span::before{
		content: "";
		display: block;
		position: absolute;
		top: -12px;
		height: 4px;
		width: 32px;
		background-color: #fff;
	}
	.left-menu span::after{
		content: "";
		display: block;
		position: absolute;
		top: 12px;
		height: 4px;
		width: 32px;
		background-color: #fff;
	}
	.header .title {
		color: #fff;
	}
</style>