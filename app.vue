<script setup lang="ts"></script>

<template>
	<div>
		<NuxtPage />
	</div>
</template>

<style>
	@font-face {
		font-family: "SauceCodePro Nerd Font";
		src: url("assets/font/SauceCodeProNerdFontPro-Regular.ttf");
	}
</style>

<style scoped>
	:global(*) {
		padding: 0;
		margin: 0;
		box-sizing: border-box;

		scroll-behavior: smooth;
	}

	/* ===== Scrollbar CSS ===== */
	/* Firefox */
	:global(*) {
		scrollbar-width: auto;
		scrollbar-color: #ffffff #000000;
	}

	/* Chrome, Edge, and Safari */
	:global(*::-webkit-scrollbar) {
		width: 12px;
	}

	:globa(*::-webkit-scrollbar-track) {
		background: #000000;
	}

	:global(*::-webkit-scrollbar-thumb) {
		background-color: #ffffff;
		border-radius: 10px;
		border: 4px solid #000000;
	}
</style>
