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
			cd your-working-dir<br />
			git apply {fileName}
		</section>
		<footer class="modal-card-foot">
			<button class="button is-success">Save changes</button>
			<button class="button">Cancel</button>
		</footer>
	</div>
</div>

<style>
	.modal-card {
		width: 1000px;
	}
</style>
