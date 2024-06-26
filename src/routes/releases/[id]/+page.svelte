<script lang="ts">
	import BrokenImageIcon from '$lib/images/broken-image.svg';
	import MinioConf from '$lib/minio/conf.js';
	import type { EventHandler } from 'svelte/elements';
	import Track from './track.svelte';
	import Comments from './comments.svelte';
	import { getContext } from 'svelte';
	import type { SessionInfos } from '$lib/session';
	import type { Readable } from 'svelte/store';

	let session: Readable<SessionInfos> = getContext('session');

	export let data;
	export let form;

	let track: null | string = form?.track ?? data.track;

	const handleBrokenImageLink: EventHandler = (e) => {
		const img = e.target as HTMLImageElement;
		img.style.display = 'none';
		img.src = BrokenImageIcon;
	};

	function showTrackCallback(id: string) {
		track = id;
	}
</script>

<div class="card">
	<div class="cover">
		<img src={BrokenImageIcon} alt="Release cover placeholder" />
		<img
			src={`${MinioConf.baseUrl}/images/${data.release.coverName}`}
			alt="Release cover"
			on:error={handleBrokenImageLink}
		/>
	</div>
	<div class="release-infos">
		<h1>{data.release.title}</h1>
		<a href={`/users/${data.release.author.id}`} class="author">
			{data.release.author.username}
		</a>
	</div>
	<div class="decription">
		{data.release.description}
	</div>
	<div class="tracks">
		{#each data.release.tracks as ctrack}
			<Track track={ctrack} {showTrackCallback} selected={track == ctrack.id} />
		{/each}
	</div>

	{#if form?.error}
		<span class="errors">
			{form?.error}
		</span>
	{/if}

	{#if track}
		<Comments loggedIn={$session} trackId={track} />
	{/if}
</div>

<style>
	.card {
		background-color: var(--overlay);
		border-radius: var(--rounding);
		padding: 3em;
		width: 60em;
		margin: auto;
		display: grid;
		grid-template-areas:
			'cover infos '
			'cover desc '
			'cover tracks '
			'comments tracks ';
		grid-template-columns: 18vw auto;
		grid-template-rows: 6vw 6vw 6vw auto;
		gap: 1em;
	}

	.release-infos {
		grid-area: infos;
	}

	.tracks {
		grid-area: tracks;
	}

	.cover {
		width: 100%;
		height: 100%;
		object-fit: cover;
		border-radius: var(--rounding);
		grid-area: cover;
		place-self: center;
		display: grid;
		aspect-ratio: 1;
	}

	.cover img {
		width: 80%;
		height: 80%;
		background-color: var(--highlight-low);
		border-radius: var(--rounding);
		object-fit: cover;
		grid-area: 1 / 1;
		place-self: center;
		color: var(--text);
	}

	.cover img * {
		color: var(--text);
	}

	.author:hover {
		text-decoration: underline;
		font-weight: bold;
	}

	.author {
		color: inherit;
		text-decoration: none;
		place-self: center;
	}

	.author {
		font-size: 20px;
	}

	.errors {
		color: var(--error);
	}
</style>
