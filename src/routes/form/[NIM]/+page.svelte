<script lang="ts">
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';
	import { BACKEND_BASE_URL, COM_STYLES, RATE_VALUES } from '$lib/constants';
	import { onMount } from 'svelte';
	import Star from '../../components/star.svelte';

	const NIM = $page.params.NIM;
	const comStyles: any = COM_STYLES;
	const rateVals: any = RATE_VALUES;
	let selectedComStyles: number = 1;
	let rating: number = 1;
	let UID: any;
	let idToken: any;
	let firstTime: boolean = true;
	let isIndicatorActive = false;
	onMount(async () => {
		if (typeof localStorage !== 'undefined') {
			UID = localStorage.getItem('uid');
			idToken = localStorage.getItem('idToken');
		}
		if (!UID || !idToken) {
			goto('/');
		}

		const res = await fetch(`${BACKEND_BASE_URL}/users/review/${NIM}/${UID}`, {
			mode: 'cors',
			method: 'GET',
			headers: {
				'Content-Type': 'application/json',
				Authorization: 'Bearer ' + idToken
			}
		});
		const data = await res.json();

		if (data.error) {
			firstTime = true;
		} else {
			firstTime = false;
			console.log(data);
			selectedComStyles = parseInt(data.data.comStyle);
			rating = data.data.rating;
		}
	});

	async function handleSubmit(e: any) {
		const body = {
			NIM,
			comStyle: selectedComStyles.toString(),
			rating,
			reviewerID: localStorage.getItem('uid')
		};
		const res = await fetch(`${BACKEND_BASE_URL}/form/${NIM}`, {
			mode: 'cors',
			method: 'POST',
			body: JSON.stringify(body),
			headers: {
				'Content-Type': 'application/json',
				Authorization: 'Bearer ' + localStorage.getItem('idToken')
			}
		});
		const data = await res.json();
		console.log(data);
	}
</script>

<h1>Review {NIM}!</h1>
<div class="form-container">
	<form on:submit|preventDefault={handleSubmit}>
		<div class="label-select">
			<label for="comStyles">Communication Style</label>
			<select bind:value={selectedComStyles} name="comStyles" id="comStyles">
				{#each comStyles as comStyle}
					<option value={comStyle.value}>{comStyle.text}</option>
				{/each}
			</select>
		</div>
		<div class="label-select">
			<label for="comStyles">Rate</label>
			<!-- <Star {rating} {isIndicatorActive} /> -->
			<select bind:value={rating} name="rating" id="rating">
				{console.log(rating)}
				{#each rateVals as rateVal}
					<option value={rateVal.value}>{rateVal.text}</option>
				{/each}
			</select>
		</div>
		<button type="submit" id="submitButton">
			{#if firstTime}
				Submit
			{:else}
				Update
			{/if}
		</button>
	</form>
</div>

<style>
	.form-container {
		display: flex;
		flex-direction: column;
		background-color: white;
		padding: 1rem;
		border-radius: 1rem;
	}
	form {
		display: flex;
		flex-direction: column;
	}
	label {
		font-weight: bold;
		margin-bottom: 0.5rem;
		margin-top: 0.5rem;
		font-size: large;
	}
	.label-select {
		display: flex;
		flex-direction: column;
		margin-top: 0.5rem;
	}
	select {
		border: none;
		outline: none;
		padding: 0.5rem;
		border-radius: 0.3rem;
		font-size: medium;
		font-weight: 400;
	}
	select:hover {
		cursor: pointer;
		background-color: #d3d3d3;
	}
	select::content {
		cursor: pointer;
	}
	#submitButton {
		margin-top: 1rem;
		width: 30%;
		align-self: center;
		border: none;
		outline: none;
		padding: 0.5rem;
		border-radius: 1rem;
		font-weight: bold;
		font-size: medium;
	}
	#submitButton:hover {
		border: none;
		outline: none;
		cursor: pointer;
	}
	#submitButton:active:focus {
		border: none;
		outline: none;
	}
</style>
