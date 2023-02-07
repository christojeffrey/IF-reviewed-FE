<script lang="ts">
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import Card from './components/card.svelte';
	import { BACKEND_BASE_URL } from '../lib/constants';

	let data: any = [];
	let isLoading: boolean = true;
	let searchQuery: any = $page.url.searchParams.get('searchQuery');
	onMount(async () => {
		// check if searchQuery
		if (searchQuery) {
			const response = await fetch(`${BACKEND_BASE_URL}/users/search?searchQuery=${searchQuery}`, {
				method: 'GET',
				headers: {
					'Content-Type': 'application/json'
				}
			});
			data = await response.json();
			if (data.length == 0) {
			}
			console.log(data);
			isLoading = false;
			return;
		} else {
			const response = await fetch(`${BACKEND_BASE_URL}/users/random`);
			data = await response.json();
			if (data.length == 0) {
			}
			isLoading = false;
			console.log(data);
		}
	});
</script>

{#if isLoading}
	<h1>Loading...</h1>
{:else if data.length != 0}
	<div class="card-list-container">
		{#each data as datum}
			<Card detail={datum} />
		{/each}
	</div>
{:else}
	<h1>Not Found</h1>
{/if}

<style>
	.card-list-container {
		/* grid following the size of the card */
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
		grid-gap: 1rem;
		justify-items: center;
	}
</style>
