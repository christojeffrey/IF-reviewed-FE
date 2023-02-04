<script lang="ts">
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import Card from './components/card.svelte';
	import { BACKEND_BASE_URL } from '../lib/constants';
	import SearchBar from './components/searchBar.svelte';
	let NIM = $page.params.NIM;
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

	async function handleSearch(e: any) {
		e.preventDefault();
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
		goto(`/?searchQuery=${searchQuery}`);
	}
</script>

<SearchBar bind:searchQuery onSearch={handleSearch} />
<p>search query : {searchQuery}</p>
{#if data.length == 0}
	<h1>Not Found</h1>
{/if}
{#each data as datum}
	<Card detail={datum} />
{/each}
