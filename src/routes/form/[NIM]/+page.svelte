<script lang="ts">
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';
	import { BACKEND_BASE_URL, COM_STYLES, RATE_VALUES } from '$lib/constants';
	import { onMount } from 'svelte';
	import Star from '../../components/starInput.svelte';
	import Amiable from '../../../assets/amiable.jpg';
	import Analytical from '../../../assets/analytical.jpg';
	import Expressive from '../../../assets/expressive.jpg';
	import Driver from '../../../assets/driving.jpg';

	const NIM = $page.params.NIM;
	const comStyles: any = COM_STYLES;
	const rateVals: any = RATE_VALUES;
	let selectedComStyles: number = 1;
	let rating: any;
	let UID: any;
	let idToken: any;
	let firstTime: boolean = true;
	let isIndicatorActive = false;
	let style = {
		styleStarWidth: 50,
		styleEmptyStarColor: '#737373',
		styleFullStarColor: '#ffd219'
	};

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

	function handleComStyleChange(e: any) {
		selectedComStyles = parseInt(e.target.value);
	}

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
			<div class="grid-img-container">
				<label>
					<input
						type="radio"
						name="comStyles"
						id="amiable"
						value="1"
						on:click={handleComStyleChange}
					/>
					<img src={Amiable} class="comStylesImg" alt="Amiable Communication Style" />
				</label>
				<label>
					<input
						type="radio"
						name="comStyles"
						id="driver"
						value="2"
						on:click={handleComStyleChange}
					/>
					<img src={Driver} class="comStylesImg" alt="Driver Communication Style" />
				</label>
				<label>
					<input
						type="radio"
						name="comStyles"
						id="expressive"
						value="3"
						on:click={handleComStyleChange}
					/>
					<img src={Expressive} class="comStylesImg" alt="Expressive Communication Style" />
				</label>
				<label>
					<input
						type="radio"
						name="comStyles"
						id="analytical"
						value="4"
						on:click={handleComStyleChange}
					/>
					<img src={Analytical} class="comStylesImg" alt="Analyitical Communication Style" />
				</label>
			</div>
		</div>
		<div class="label-select">
			<label for="comStyles">Rate</label>
			<Star {isIndicatorActive} {style} bind:rating />
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
	.grid-img-container {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		grid-template-rows: repeat(2, 1fr);
		grid-gap: 1rem;
		align-items: center;
		justify-items: center;
	}
	.comStylesImg {
		width: 100%;
		height: 100%;
		object-fit: cover;
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
	label > input {
		/* Menyembunyikan radio button */
		visibility: hidden;
		position: absolute;
	}
	label > input + img {
		/* style gambar */
		cursor: pointer;
		border: 2px solid transparent;
	}
	label > input:checked + img {
		/* (RADIO CHECKED) style gambar */
		border: 2px solid #f00;
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
		background-color: #d3d3d3;
	}
	#submitButton:active:focus {
		border: none;
		outline: none;
	}
</style>
