<script>
	export let isOpen = true;
	export let fileName;
</script>

<div class="modal" class:is-active={isOpen}>
	<div class="modal-background" on:click={() => (isOpen = false)} />
	<div class="modal-card">
		<header class="modal-card-head">
			<p class="modal-card-title">How to apply the downloaded git patch file?</p>
			<button class="delete" aria-label="close" on:click={() => (isOpen = false)} />
		</header>
		<section class="modal-card-body">
			<pre>
cd your-working-dir
git apply --reject --whitespace=fix {fileName}
			</pre>
		</section>
		<footer class="modal-card-foot">
			<button
				class="button is-success"
				on:click={() => navigator.clipboard.writeText(`git apply --reject --whitespace=fix ${fileName}`)}
				>Copy Git Command</button
			>
			<button class="button" on:click={() => (isOpen = false)}>Cancel</button>
		</footer>
	</div>
</div>

<style>
	.modal-card {
		width: 1000px;
	}
</style>
