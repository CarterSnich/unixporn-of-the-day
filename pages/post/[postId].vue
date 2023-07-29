<script setup lang="ts">
	const route = useRoute();
	const { data: post } = await useFetch(
		`https://www.reddit.com/api/info.json?id=${route.params.postId}`,
		{
			transform: (postData: any) => {
				return postData.data.children[0];
			},
		}
	);

	const isInfoShown: Ref = ref(false);
	const zoomValue: Ref = ref(100);

	const zoomInOut = (newVal: number) => {
		if (newVal == 20 && zoomValue.value < 200) {
			zoomValue.value += newVal;
		}
		if (newVal == -20 && zoomValue.value > 100) {
			zoomValue.value += newVal;
		}
	};
</script>

<template>
	<main>
		<header class="header">
			<a href="#" class="go-back" @click="$router.back()"></a>
			<span class="score">󰯎 {{ post.data.score }}</span>
			<h1 class="title">{{ post.data.title }}</h1>
			<a
				class="permalink"
				:href="`https://reddit.com${post.data.permalink}/`"
				target="_blank"
			>
				
			</a>

			<button
				class="btn-show-info"
				type="button"
				@click="isInfoShown = !isInfoShown"
			>
				
			</button>
		</header>

		<div class="content">
			<div class="info">
				<div>
					<span>
						<a
							class="author-link"
							:href="`https://www.reddit.com/user/${post.data.author}/`"
							target="_blank"
						>
							<small>u/{{ post.data.author }}</small>
						</a>
						<img
							v-for="reward in post.data.all_awardings"
							:src="reward.resized_icons[0].url"
							:alt="reward.name"
							:title="reward.description"
						/>
					</span>
					<button
						type="button"
						class="btn-hide-info"
						@click="isInfoShown = false"
					>
						
					</button>
					<p class="selftext-html">{{ post.data.selfhtml }}</p>
				</div>
			</div>

			<div class="preview">
				<img
					:src="post.data.url_overridden_by_dest"
					:class="`zoom-in-${zoomValue}`"
					loading="lazy"
				/>

				<div class="zoom-controls">
					<button @click="zoomInOut(20)"></button>
					<span>{{ zoomValue }}%</span>
					<button @click="zoomInOut(-20)"></button>
				</div>
			</div>
		</div>
	</main>
</template>

<style scoped>
	main {
		background-color: black;
		font-family: "SauceCodePro Nerd Font";
		color: white;
		height: 100vh;

		display: grid;
		grid-template-rows: min-content auto;
		grid-template-areas:
			"header"
			"content";
		gap: 0.75rem;
		padding: 0.75rem;
	}

	/* HEADER */
	.header {
		grid-area: header;

		display: flex;
		flex-direction: row;
		align-items: center;
		gap: 0.75rem;
	}

	.score {
		white-space: nowrap;
	}

	.go-back,
	.permalink {
		color: white;
		text-decoration: none;
	}

	.go-back:hover,
	.permalink:hover {
		text-decoration: underline;
	}

	.btn-show-info {
		margin-inline: auto 0.75rem;

		font-family: inherit;
		font-size: 1.75rem;
		line-height: 1.5rem;
		color: white;

		height: fit-content;
		background-color: transparent;
		border: 0;
		border-radius: 1.5rem;
	}

	.btn-show-info:hover {
		box-shadow: 0px 0px 8px white;
	}

	.btn-show-info:active {
		filter: brightness(80%);
	}

	/* CONTENT */

	.content {
		grid-area: content;
		display: flex;
		flex-direction: row;
		overflow: hidden;
	}

	.info {
		position: relative;
	}

	.info > div {
		position: absolute;
		left: -300px;
		z-index: 1;

		width: 300px;
		height: 100%;

		padding: 0.75rem;
		color: white;
		background-color: black;
		border: 1px solid white;
		border-radius: 0.75rem;

		transition: left 0.3s ease-in-out;
	}

	.info.show > div {
		left: 0;
	}

	.info .author-link {
		color: white;
		text-decoration: none;
	}

	.info .author-link:hover {
		text-decoration: underline;
	}

	.info .btn-hide-info {
		position: absolute;
		top: 0.75rem;
		right: 0.75rem;

		font-family: inherit;
		font-size: 1.75rem;
		line-height: 1.5rem;
		color: white;

		height: fit-content;
		background-color: transparent;
		border: 0;
		border-radius: 1.5rem;
	}

	.info .btn-hide-info:hover {
		text-shadow: 0px 0px 8px white;
	}

	.info .btn-hide-info:active {
		filter: brightness(80%);
	}

	.info > div > span {
		display: flex;
		flex-direction: row;
		align-items: center;
		gap: 0.25rem;
	}

	.info .selftext-html {
		margin-top: 0.75rem;
	}

	.preview {
		flex-grow: 1;
		cursor: grab;
		overflow: auto;
	}

	.preview::after,
	.preview::before {
		content: "";
		width: 0;
		height: 0;
		display: none;
		clear: none;
	}

	.preview > img {
		display: block;
		height: 100%;
		width: 100%;
		object-fit: contain;
	}

	.preview > img.zoom-in-120 {
		height: 120%;
		width: 120%;
	}

	.preview > img.zoom-in-140 {
		height: 140%;
		width: 140%;
	}

	.preview > img.zoom-in-160 {
		height: 160%;
		width: 160%;
	}

	.preview > img.zoom-in-180 {
		height: 180%;
		width: 180%;
	}

	.preview > img.zoom-in-200 {
		height: 200%;
		width: 200%;
	}

	.zoom-controls {
		position: fixed;
		bottom: 1.75rem;
		left: 1.75rem;

		display: flex;
		flex-direction: row;
		align-items: center;
	}

	.zoom-controls > button {
		color: white;
		font-family: "SauceCodePro Nerd Font";
		font-size: 1.5rem;
		background-color: transparent;
		border: none;
		padding: 0.25rem 0.5rem;
	}

	.zoom-controls > button:hover {
		text-shadow: 0px 0px 8px white;
	}

	.zoom-controls > button:active {
		filter: brightness(80%);
	}
</style>
