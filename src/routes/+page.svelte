<script lang="ts">
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import Card from './components/card.svelte';
	import { BACKEND_BASE_URL } from '../lib/constants';
	import SearchBar from './components/searchBar.svelte';

	let NIM = $page.params.NIM;
	let data: any = [];
	let searchQuery: any = '';
	onMount(async () => {
		const response = await fetch(`${BACKEND_BASE_URL}/users/random`);
		data = await response.json();
		if (data.length == 0) {
			goto('/404');
		}
		console.log(data);
	});

	function handleSearch(e: any) {
		e.preventDefault();
		goto(`/`);
	}
</script>

<SearchBar bind:searchQuery onSearch={handleSearch} />
<p>search query : {searchQuery}</p>
{#if data.length == 0}
	<h1>Not Found</h1>
{/if}
{#each data as datum}
	<Card NIM={datum.NIM} />
{/each}
