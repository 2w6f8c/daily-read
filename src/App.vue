<template>
	<div id="app" class="app" :class="{'night': theme.nightMode}">
		<div class="left" ref="left">
			<v-nav-left></v-nav-left>
		</div>
		<div class="main-wrapper" ref="mainWrapper">
			<transition :name="transitionName">
				<router-view></router-view>
			</transition>
		</div>
		<div class="right" ref="right">
			<v-nav-right></v-nav-right>
		</div>
		<div class="bottom-wrapper" ref="bottomWrapper">
			<v-bottom></v-bottom>
		</div>
		<v-mask></v-mask>
	</div>
</template>

<script>
	import {
		mapState,
		mapMutations
	} from 'vuex';


	import collectionStore from '@/utils/collectionStore';

	import navLeft from '@/components/left/left';
	import navRight from '@/components/right/right';
	import bottom from '@/components/bottom/bottom';
	import main from '@/components/main/main';
	import mask from '@/components/mask/mask';


	export default {
		name: 'App',
		mounted() {
			window.addEventListener('beforeunload', () => {
				// dump collection to localStorage
				collectionStore.save(this.$store.state.collection);
			});
		},
		data() {
			return {
				// change(s) should alse be applied to corresponding variable(s) in variable.scss 
				navLeftWidth: 36, //vw
				navRightWidth: 20, //vw
				padBottomHeight: 26, //vh
				//transition
				transitionName: ''
			};
		},
		computed: {
			...mapState([
				'navLeftOn',
				'navRightOn',
				'padBottomOn',
				'theme'
			])
		},
		components: {
			'v-nav-left': navLeft,
			'v-nav-right': navRight,
			'v-bottom': bottom,
			'v-main': main,
			'v-mask': mask
		},
		watch: {
			// for animation
			$route(to, from) {
				if (to.meta.index > from.meta.index) {
					this.transitionName = '';
				} else {
					this.transitionName = 'fade';
				}
			},
			navLeftOn(newValue) {
				let left = this.$refs.left;
				let mainWrapper = this.$refs.mainWrapper;
				let articleWrapper = this.$refs.articleWrapper;
				if (newValue) {
					left.style.left = '0';
					mainWrapper.style.left = this.navLeftWidth + 'vw';
					mainWrapper.style.overflowY = 'hidden'; //prevent mainWrapper from scrolling
				} else {
					left.style.left = -this.navLeftWidth + 'vw';
					mainWrapper.style.left = '0';
					mainWrapper.style.overflowY = '';
				}
			},
			navRightOn(newValue) {
				let right = this.$refs.right;
				let mainWrapper = this.$refs.mainWrapper;
				if (newValue) {
					right.style.left = (100 - this.navRightWidth) + 'vw';
					mainWrapper.style.overflowY = 'hidden'; //prevent mainWrapper from scrolling
				} else {
					right.style.left = '100vw';
					mainWrapper.style.overflowY = '';
				}
			},
			padBottomOn(newValue) {
				let bottomWrapper = this.$refs.bottomWrapper;
				let mainWrapper = this.$refs.mainWrapper;
				if (newValue) {
					bottomWrapper.style.top = (100 - this.padBottomHeight) + 'vh';
					mainWrapper.style.overflowY = 'hidden'; //prevent mainWrapper from scrolling
				} else {
					bottomWrapper.style.top = '100vh';
					mainWrapper.style.overflowY = 'hidden'; //prevent mainWrapper from scrolling
				}
			}
		}
	}
</script>

<style lang="scss">
	@import 'assets/fonts/iconfont.css';
	@import 'assets/scss/index.scss';

	.app {
		font-family: 'Avenir', Helvetica, Arial, sans-serif;
		background: $basic-color;

		&.night {
			background: $basic-color-night;
		}

		.left {
			z-index: 200;
			position: fixed;
			top: 0;
			left: -$nav-left-width;
			background: $nav-color;
			width: $nav-left-width;
			height: 100%;
			transition: $left-main-transition;
		}

		.main-wrapper {
			position: absolute;
			top: 0;
			left: 0;
			width: 100vw;
			height: 100vh;
			transition: $left-main-transition;
		}

		.right {
			position: fixed;
			z-index: 200;
			width: $nav-right-width;
			height: 100%;
			left: 100vw;
			top: 0;
			background: $nav-color;
			transition: linear 0.2s;
		}

		.bottom-wrapper {
			z-index: 200;
			position: fixed;
			top: 100vh;
			left: 0;
			width: 100vw;
			height: $pad-bottom-height;
			transition: ease-out 0.2s;
		}

		//transition
		.fade-enter-active,
		.fade-leave-active {
			transition: opacity .8s
		}

		.fade-enter,
		.fade-leave-active {
			opacity: 0.7
		}

	}
</style>
