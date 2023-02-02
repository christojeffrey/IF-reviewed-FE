<!-- create a search bar that on click will hit /search -->
<script lang="ts">
	export let searchQuery: any;
	export let onSearch: any;
</script>

<!-- with icon on the right as opposed to button -->
<div>
	<input type="text" bind:value={searchQuery} placeholder="Search" />
	<button on:click={onSearch}> search </button>
</div>
