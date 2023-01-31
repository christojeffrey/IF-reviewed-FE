<script lang="ts">
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';
	import Card from './components/card.svelte';

	let NIM = $page.params.NIM;
	let data: any = [
		{
			NIM: '123'
		},
		{
			NIM: '456'
		}
	];

	// onMount(async () => {
	//     const response = await fetch(`http://localhost:3000/api/form/${NIM}`);
	//     data = await response.json();
	//     if (data.length == 0) {
	//         goto('/404');
	//     }
	// });
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
{#each data as datum}
	<Card NIM={datum.NIM} />
{/each}
