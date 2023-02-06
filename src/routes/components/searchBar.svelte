<!-- create a search bar that on click will hit /search -->
<script lang="ts">
	export let searchQuery: any;
	export let onSearch: any;
	// import image
	import searchIcon from '../../assets/search.png';
</script>

<!-- with icon on the right as opposed to button -->
<div>
	<div class="input-container">
		<input type="text" bind:value={searchQuery} placeholder="Search" />

		<button
			on:click={(e) => {
				// check if searchQuery is empty
				if (searchQuery) {
					// if not empty, call onSearch
					onSearch(e);
				}
			}}
		>
			<img class="search-icon" src={searchIcon} alt="search" /></button
		>
	</div>
</div>

<style>
	button {
		background: none;
		color: inherit;
		border: none;
		padding: 0;
		font: inherit;
		cursor: pointer;
		outline: inherit;
	}
	.search-icon {
		width: 1rem;
		height: 1rem;
	}
	.input-container {
		/* rectangle with rounded */
		border-radius: 1.5rem;
		box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
		padding: 1rem;
		width: fit-content;
		/* flexbox */
		display: flex;
		flex-direction: row;
		align-items: center;
		/* margin */
		background-color: white;
	}
	/* remove input attribute */
	input {
		border: none;
		/* prevent border on focus */
		outline: none;
	}
</style>
