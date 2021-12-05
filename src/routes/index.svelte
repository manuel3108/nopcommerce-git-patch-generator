<script>
	import DownloadPopup from '$lib/DownloadPopup.svelte';
	import TagSelect from '$lib/TagSelect.svelte';
	import { onMount } from 'svelte';

	let tags = [];
	let startSha;
	let endSha;
	let downloadUrl = '';
	let downloading = false;
	let fileName;

	$: {
		if (startSha && endSha) {
			fileName = `${startSha}_${endSha}.patch`;
			getUrl();
		}
	}

	onMount(async () => {
		const response = await fetch(
			'https://api.github.com/repos/nopSolutions/nopCommerce/tags?per_page=100'
		);
		tags = await response.json();
	});

	async function getUrl() {
		const response = await fetch(
			'https://api.github.com/repos/manuel3108/nopcommerce-git-patch-generator/releases/latest'
		);
		const releases = await response.json();
		const assets = releases.assets;
		console.log(assets);

		const asset = assets.find((asset) => asset.name === fileName);

		if (asset !== undefined) {
			downloadUrl = asset.browser_download_url;
			console.log(downloadUrl);
		}
	}
</script>

<div class="box split">
	<div class="field">
		<label class="label">Start tag (oldest)</label>
		<div class="control">
			<TagSelect {tags} bind:value={startSha} />
		</div>
	</div>
	<div class="field">
		<label class="label">End tag (newest)</label>
		<div class="control">
			<TagSelect {tags} bind:value={endSha} />
		</div>
	</div>
</div>

<div class="box split">
	<div class="field">
		<label class="label">Start commit (oldest)</label>
		<div class="control">
			<input bind:value={startSha} />
		</div>
	</div>
	<div class="field">
		<label class="label">End commit (newest)</label>
		<div class="control">
			<input bind:value={endSha} />
		</div>
	</div>
</div>

<div class="box">
	{#if !startSha || !endSha}
		Please select by tags or commits
	{:else}
		<button class="button is-primary" href={downloadUrl} on:click={() => (downloading = true)}
			>Download</button
		>
	{/if}
</div>

{#if downloading}
	<DownloadPopup bind:isOpen={downloading} {fileName} />
{/if}

<style>
	.box.split .field {
		display: inline-block;
		width: 49.8%;
	}

	.box.split .field .control :global(select),
	.box.split .field .control :global(input) {
		width: 100%;
	}
</style>
