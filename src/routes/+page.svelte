<script lang="ts">
	import { page } from '$app/stores';
	import { afterUpdate, beforeUpdate, onMount } from 'svelte';
	import Card from './components/card.svelte';
	import { BACKEND_BASE_URL } from '../lib/constants';
	import { goto } from '$app/navigation';

	let userData: any = [];

	// set isLoading to true whenever the url changed

	let searchQuery: any = $page.url.searchParams.get('searchQuery');
	let isLoading: boolean = true;

	$: if ($page.url.searchParams.get('searchQuery')) {
		searchQuery = $page.url.searchParams.get('searchQuery');
		console.log('searchQuery');
		console.log(searchQuery);
		updateUserDataBasedOnQuery(searchQuery);
	}
	$: if (userData && userData.length != 0) {
		isLoading = false;
	}
	function updateUserDataBasedOnQuery(query: string) {
		isLoading = true;

		fetch(`${BACKEND_BASE_URL}/users/search?searchQuery=${query}`, {
			method: 'GET',
			headers: {
				'Content-Type': 'application/json'
			}
		})
			.then((response) => {
				return response.json();
			})
			.then((data) => {
				console.log('data');
				console.log(data);
				isLoading = false;
				userData = data;
			})
			.catch((error) => {
				console.log(error);
			});
	}

	onMount(async () => {
		// check if there is error url param
		if ($page.url.searchParams.get('error')) {
			alert($page.url.searchParams.get('error'));
			// remove the error url param
			goto($page.url.pathname);
		}
		if (searchQuery) {
			updateUserDataBasedOnQuery(searchQuery);
		} else {
			isLoading = true;
			const response = await fetch(`${BACKEND_BASE_URL}/users/random`);
			userData = await response.json();
			if (userData.length == 0) {
			}
			isLoading = false;
			console.log(userData);
		}
	});
</script>

{#if isLoading}
	<h1>Loading...</h1>
{:else if userData.length != 0}
	<div class="card-list-container">
		{#each userData as datum}
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
