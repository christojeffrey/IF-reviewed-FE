<script lang="ts">
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import Card from './components/card.svelte';
	import { BACKEND_BASE_URL } from '../lib/constants';

	let data: any = [];
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
			return;
		} else {
			const response = await fetch(`${BACKEND_BASE_URL}/users/random`);
			data = await response.json();
			if (data.length == 0) {
			}
			console.log(data);
		}
	});
</script>

{#if data.length == 0}
	<h1>Not Found</h1>
{/if}
{#each data as datum}
	<Card detail={datum} />
{/each}
