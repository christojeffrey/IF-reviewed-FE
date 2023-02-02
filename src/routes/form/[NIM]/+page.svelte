<script lang="ts">
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';
	import { BACKEND_BASE_URL, COM_STYLES, RATE_VALUES } from '$lib/constants';
	import { onMount } from 'svelte';

	const NIM = $page.params.NIM;
	const comStyles: any = COM_STYLES;
	const rateVals: any = RATE_VALUES;
	let selectedComStyles: number = 1;
	let rating: number = 1;
	onMount(async () => {
		const res = await fetch(`${BACKEND_BASE_URL}/${NIM}`);
		const data = await res.json();
		if (data.length == 0) {
			goto('/404');
		}
		console.log(data);
	});

	async function handleSubmit(e: any) {
		const body = {
			NIM,
			comStyle: selectedComStyles,
			rating,
			reviewerID: 'test11ing'
		};
		const res = await fetch(`${BACKEND_BASE_URL}/form/${NIM}`, {
			mode: 'cors',
			method: 'POST',
			body: JSON.stringify(body),
			headers: {
				'Content-Type': 'application/json'
			}
		});
		const data = await res.json();
		console.log(data);
	}
</script>

<h1>nim</h1>
<p>{NIM}</p>
<div class="form-container">
	<form on:submit|preventDefault={handleSubmit}>
		<label for="comStyles">Communication Style:</label>
		<select bind:value={selectedComStyles} name="comStyles" id="comStyles">
			{console.log(selectedComStyles)}
			{#each comStyles as comStyle}
				<option value={comStyle.value}>{comStyle.text}</option>
			{/each}
		</select>
		<label for="comStyles">Rate:</label>
		<select bind:value={rating} name="rating" id="rating">
			{console.log(rating)}
			{#each rateVals as rateVal}
				<option value={rateVal.value}>{rateVal.text}</option>
			{/each}
		</select>
		<button type="submit">Submit</button>
	</form>
</div>

<style>
	.form-container {
		display: flex;
		flex-direction: row;
	}
	form {
		display: flex;
		flex-direction: column;
	}
</style>
