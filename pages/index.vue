<script setup lang="ts">
	useHead({
		title: "unixporn-of-the-day",
	});

	const SUBREDDIT: String = "unixporn";
	const LIMIT: number = 100;

	const { data: listings } = useFetch(
		`https://www.reddit.com/r/${SUBREDDIT}/hot.json?limit=${LIMIT}`,
		{
			transform: (listings: any) => {
				let posts: any = listings.data.children;
				posts = posts.filter((post: any) =>
					Object.values(post.data.link_flair_richtext[0]).includes("Screenshot")
				);
				posts.length = 30;
				return posts;
			},
		}
	);

	const parseImgUrl = (_url: string): string => {
		const url = new URL(_url);
		if (url.hostname === "imgur.com") {
			return `https://i.imgur.com/${url.pathname.replace("/a/", "")}.png`;
		}
		return url.href;
	};
</script>

<template>
	<main>
		<NuxtLink
			v-for="post in listings"
			class="post"
			:to="`/post/${post.kind}_${post.data.id}`"
		>
			<img
				v-if="post.data.url_overridden_by_dest"
				:src="parseImgUrl(post.data.url_overridden_by_dest)"
			/>
			<div class="post-heading">
				<div>
					<p>{{ post.data.title }}</p>
					<p class="post-score">󰯎{{ post.data.score }}</p>
				</div>
			</div>
		</NuxtLink>
	</main>
</template>

<style scoped>
	main {
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		font-family: "SauceCodePro Nerd Font", monospace;
		color: white;
		background-color: black;

		overflow: auto;
		scroll-snap-type: y proximity;
	}

	.post {
		width: 50%;
		height: 50vh;

		display: grid;
		place-content: stretch;
		isolation: isolate;
		overflow: hidden;
		color: white;
		text-decoration: none;

		scroll-snap-align: start;
	}

	.post > img,
	.post > .post-heading {
		grid-column: 1/-1;
		grid-row: 1/-1;
	}

	.post > img {
		object-fit: cover;
		object-position: center;
		width: 100%;
		height: 100%;
		overflow: hidden;
	}

	.post-heading {
		display: flex;
		flex-direction: column;
		justify-content: flex-end;

		background: rgb(0, 0, 0);
		background: linear-gradient(
			0deg,
			rgba(0, 0, 0, 1) 0%,
			rgba(0, 0, 0, 0.5) 100%
		);
		padding: 0.75rem;
		opacity: 0;

		transition: opacity 0.3s ease-in-out;
	}

	.post:hover > .post-heading {
		opacity: 1;
	}

	.post-heading > div {
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: flex-start;
	}

	.post-score {
		display: flex;
		flex-direction: row;
		align-items: center;

		white-space: nowrap;
	}
</style>
