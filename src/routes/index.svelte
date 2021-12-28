<script>
	import DownloadPopup from '$lib/DownloadPopup.svelte';
	import TagSelect from '$lib/TagSelect.svelte';
	import { onMount } from 'svelte';
	import newGithubIssueUrl from 'new-github-issue-url';

	let tags = [];
	let startSha;
	let endSha;
	let downloadUrl = '';
	let downloading = false;
	let fileName;
	let downloadExists = false;

	$: {
		if (startSha && endSha) {
			fileName = `${startSha}_${endSha}.patch`;
			getUrl();
		}
	}

	onMount(async () => {
		const urlParams = new URLSearchParams(window.location.search);
		startSha = urlParams.get('startSha');
		endSha = urlParams.get('endSha');
		console.log(startSha, endSha);

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

		const asset = assets.find((asset) => asset.name === fileName);

		if (asset !== undefined) {
			downloadUrl = asset.browser_download_url;
			downloadExists = true;
		} else {
			downloadExists = false;
		}
	}

	function generateGithubIssueUrl() {
		const url = newGithubIssueUrl({
			user: 'manuel3108',
			repo: 'nopcommerce-git-patch-generator',
			title: `Patch: ${startSha}_${endSha}`,
			body: `
After creating this issue an github action will be triggered to generate the patch just for you.
Additionally a bot will comment on the issue and inform you when the patch is ready.
After the patch has been created, you will be redirected back to the website to download the patch.
Overall the process should not take you more then five minutes.
			`
		});
		return url;
	}
</script>

<div class="box split">
	<h1 class="title">NopCommerce Patch Generator</h1>
	<p>
		This tool generates a patches for NopCommerce. In order to download your patch file, please
		select the NopCommerce release from an dropdown, or enter the start and end sha manually.
	</p>
	<br />
	<ul class="notes">
		Aditional notes:<br />
		<li>
			The patch file will be created relatively to the src directory of the nopcommerce repository.
			Please be sure to apply your patch in the root of your project.
		</li>
		<li>The patch will also include update binary files such as images and dll's.</li>
		<li>
			If the patch file for your selection does not exist, we will redirect you to Github to create
			an issue. From there on an automatic process takes places, and will generete the patch file
			you. After the patch file is generated, you will be redirected back to the website to download
			the patch.
		</li>
	</ul>
</div>

<div class="box split">
	<div class="field">
		<label class="label">Start tag (older release)</label>
		<div class="control">
			<TagSelect {tags} bind:value={startSha} />
		</div>
	</div>
	<div class="field">
		<label class="label">End tag (newer release)</label>
		<div class="control">
			<TagSelect {tags} bind:value={endSha} />
		</div>
	</div>
</div>

<div class="box split">
	<div class="field">
		<label class="label">Start commit (older commit)</label>
		<div class="control">
			<input bind:value={startSha} />
		</div>
	</div>
	<div class="field">
		<label class="label">End commit (newer commit)</label>
		<div class="control">
			<input bind:value={endSha} />
		</div>
	</div>
</div>

<div class="box">
	{#if !startSha || !endSha}
		Please select by tags or commits, in order to determine the availability of your patch file.
	{:else if !downloadExists}
		A patch file for your selected commit range does not exist at the moment.<br />
		But we would be more then happy to create it for you.<br />
		We have created an automated process for you, which only takes a few minutes and will require you
		to create an issue on github.<br />
		We will guide you throgh the process from here on.<br />
		<a href={generateGithubIssueUrl()} class="button is-primary">Create an issue on Github!</a>
	{:else}
		<a class="button is-primary" href={downloadUrl} on:click={() => (downloading = true)}
			>Download</a
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

	.notes {
		color: gray;
		font-size: smaller;
		list-style: unset;
		list-style-position: inside;
	}
</style>
