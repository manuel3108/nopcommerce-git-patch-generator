<script>
	export let tags = [];
	export let value = '';
</script>

<select class="select" bind:value>
	<option disabled selected hidden value="">---</option>
	{#each tags as tag}
		<option value={tag.commit.sha}>{tag.name}</option>
	{/each}
</select>
