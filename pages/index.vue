<script setup lang="ts">
	useHead({
		title: "unixporn-of-the-day",
	});

	const SUBREDDIT: String = "unixporn";
	const LIMIT: number = 200;

	const { data: listings } = useFetch(
		`https://www.reddit.com/r/${SUBREDDIT}/hot.json?limit=${LIMIT}`,
		{
			headers: { "User-Agent": "My Reddit API Client" },
			transform: (listings: any) => {
				let posts: any = listings.data.children;
				posts = posts.filter((post: any) => {
					return (
						Object.values(post.data.link_flair_richtext[0]).includes(
							"Screenshot"
						) && post.data.preview.enabled
					);
				});
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
				loading="lazy"
			/>
			<div class="post-heading">
				<div>
					<span class="post-title">{{ post.data.title }}</span>
					<span class="post-score">󰯎{{ post.data.score }}</span>
				</div>
			</div>
		</NuxtLink>
	</main>
</template>

<style scoped>
	main {
		font-family: "SauceCodePro Nerd Font", monospace;
		background-color: black;

		padding: 1.25rem;
		column-count: 4;
		column-gap: 1.25rem;
	}

	.post {
		display: grid;
		margin-bottom: 1.25rem;
		color: white;
		text-decoration: none;
	}

	.post > img {
		border-radius: 0.75rem;
	}

	.post > img,
	.post > .post-heading {
		grid-column: 1/-1;
		grid-row: 1/-1;
	}

	.post > img {
		object-fit: contain;
		width: 100%;
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

	.post-title {
		word-break: break-word;
	}
	.post-score {
		display: flex;
		flex-direction: row;
		align-items: center;

		white-space: nowrap;
	}
</style>
