<template>
	<div>
		<div class="gradient">
			<div class="home-wrapper">
				<div class="left">
					<h1>{{ 'home_heading' | t }}</h1>
					<h2>{{ 'home_subheading' | t }}</h2>
					<div class="button" @click="login">{{ 'home_cta' | t }}</div>
				</div>
				<div class="right">
					<img src="/img/posts.png" />
				</div>
			</div>
		</div>
		<div class="home-wrapper">
			<div class="built-with">
				<h3>{{ 'home_builtwith' | t }}</h3>
				<h4>{{ 'home_builtwith_subheading' | t }}</h4>
				<div class="cards">
					<div class="card">
						<img src="/img/card-temporary.png" />
						<div class="title">A wonderful card highlighting your Phantom-built blog</div>
						<div class="more">{{ 'home_builtwith_cta' | t }}</div>
					</div>
					<div class="card">
						<img src="/img/card-temporary.png" />
						<div class="title">A wonderful card highlighting your Phantom-built blog</div>
						<div class="more">{{ 'home_builtwith_cta' | t }}</div>
					</div>
					<div class="card">
						<img src="/img/card-temporary.png" />
						<div class="title">A wonderful card highlighting your Phantom-built blog</div>
						<div class="more">{{ 'home_builtwith_cta' | t }}</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
	import api from '@/service/safe/api';

	export default {
		name: 'home',
		methods: {
			login: function() {
				api.authenticate(false).then(response => {
					this.$root.$data.authenticated = true;

					api.getCurrentDomain().then(domain => {
						this.$root.$data.domain = domain;
						this.$router.push("/app");
					});
				});
			}
		}
	}
</script>

<style scoped lang="scss">
	.gradient {
		clear: both;
		padding: 80px 0;
		font-size: 0;
		background: linear-gradient(145deg, #3ca6ce, #7a36c8, #c836a9);
		overflow: hidden;
	}

	.left, .right {
		display: inline-block;
		vertical-align: top;
		width: calc(50% - 20px);
	}

	.left {
		margin-right: 40px;

		.button {
			font-size: 16px;
		}
	}

	.right img {
		width: 960px;
		border-radius: 10px;
		box-shadow: 0 0 35px #7343c9;
	}

	h1 {
		margin: 0;
		color: #fff;
		line-height: 60px;
		font-size: 55px;
		font-weight: normal;
	}

	h2 {
		color: #fff;
		line-height: 25px;
		font-size: 20px;
		font-weight: normal;
	}

	.built-with {
		padding: 40px 0;
		text-align: center;

		h3 {
			margin: 0 0 10px 0;
			font-size: 32px;
		}

		h4 {
			margin: 0 0 40px 0;
			font-size: 18px;
			opacity: .75;
		}

		.cards {
			font-size: 0;

			.card {
				display: inline-block;
				vertical-align: top;
				width: 25%;
				padding: 20px;
				background-color: #fff;
				box-shadow: 0 2px 10px #e1e1e1;
				transform: scale(1);
				cursor: pointer;
				transition: 0.2s all;

				&:first-child {
					margin-right: 4.5%;
				}

				&:last-child {
					margin-left: 4.5%;
				}

				&:hover {
					transform: scale(1.05);
					box-shadow: 0 2px 15px #e1e1e1;
				}

				img {
					width: 100%;
				}

				.title {
					padding: 20px 0;
					text-align: left;
					font-size: 18px;
					font-weight: bold;
				}

				.more {
					position: relative;
					text-align: left;
					font-size: 13px;
					font-weight: bold;
					color: #2d6cad;
					text-transform: uppercase;

					&:after {
						display: inline-block;
						position: absolute;
						right: 0;
						top: 0;
						content: "→";
						font-size: 24px;
						color: #2d6cad;
						line-height: 13px;
					}
				}
			}
		}
	}

	@media (max-width: 767px) {
		.gradient {
			padding: 40px 0 60px 0;
		}

		.left, .right {
			display: block;
			width: 100%;

			img {
				width: 200%;
				max-width: 600px;
			}

			.button {
				font-size: 14px;
			}
		}

		.left {
			padding-bottom: 20px;
		}

		h1 {
			font-size: 33px;
			line-height: 40px;
		}

		h2 {
			line-height: 25px;
			font-size: 17px;
		}

		.built-with {
			h3 {
				font-size: 28px;
			}

			.cards {
				.card {
					width: 100%;
					max-width: 360px;
					margin: 0 auto 20px auto;

					&:first-child, &:last-child {
						margin: 0 auto 20px auto;
					}
				}
			}
		}
	}
</style>