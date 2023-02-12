<script lang="ts">
	import { goto } from '$app/navigation';
	import { page } from '$app/stores';
	import { BACKEND_BASE_URL, COM_STYLES, RATE_VALUES } from '$lib/constants';
	import { onMount } from 'svelte';
	import { start_hydrating } from 'svelte/internal';
	import Star from '../../components/starInput.svelte';

	const NIM = $page.params.NIM;
	$: selectedComStyles = 1;
	let rating: any;
	let UID: any;
	let idToken: any;
	let firstTime: boolean = true;
	let isIndicatorActive = false;
	let style = {
		styleStarWidth: 60,
		styleEmptyStarColor: '#ffffff',
		styleFullStarColor: '#ffd219'
	};
	let responseData: any = null;

	let status: any = null;
	let isLoading = false;
	$: if (responseData) {
		status = 'success!';
		isLoading = false;
	}
	$: if (isLoading) {
		status = 'loading...';
	}
	let isMounted = false;
	onMount(async () => {
		if (typeof localStorage !== 'undefined') {
			UID = localStorage.getItem('uid');
			idToken = localStorage.getItem('idToken');
		}

		if (!UID || !idToken) {
			goto('/?error=You need to login first');
			return;
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
			console.log(data);
		}
		isMounted = true;
	});

	function handleComStyleChange(e: any) {
		selectedComStyles = parseInt(e.target.value);
		console.log(selectedComStyles);
	}

	async function handleSubmit(e: any) {
		isLoading = true;
		const body = {
			NIM,
			comStyle: selectedComStyles.toString(),
			rating: parseInt(rating),
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
		responseData = await res.json();
		console.log(responseData);
	}

	let comStyles: any = {
		1: {
			title: 'ANALYTICAL',
			trait1: 'serious',
			trait2: 'critical',
			bg: 'analytical-bg',
			color: 'analytical-color'
		},
		2: {
			title: 'DRIVER',
			trait1: 'competitive',
			trait2: 'ambitious',
			bg: 'driver-bg',
			color: 'driver-color'
		},
		3: {
			title: 'AMIABLE',
			trait1: 'friendly',
			trait2: 'helpful',
			bg: 'amiable-bg',
			color: 'amiable-color'
		},
		4: {
			title: 'EXPRESSIVE',
			trait1: 'outgoing',
			trait2: 'energetic',
			bg: 'expressive-bg',
			color: 'expressive-color'
		}
	};
</script>

<!-- check isMounted -->
{#if !isMounted}
	<div class="loading">Loading...</div>
{:else}
	<h1>Review {NIM}!</h1>
	<div class="form-container">
		<form on:submit|preventDefault={handleSubmit}>
			<div style="display: grid; justify-items: center;">
				<div class="grid-container">
					<!-- for earch comStyles -->
					{#each Object.keys(comStyles) as comStyle}
						<div
							class="label-container"
							id={comStyles[comStyle].bg}
							style={selectedComStyles === parseInt(comStyle)
								? 'background-color: #ffd219; color: #000000;'
								: ''}
						>
							<label class="comStyle-label">
								<input
									type="radio"
									name="comStyles"
									id={comStyle}
									value={comStyle}
									on:click={handleComStyleChange}
								/>
								<h3>{comStyles[comStyle].title}</h3>
								<p>{comStyles[comStyle].trait1}</p>
								<p>{comStyles[comStyle].trait2}</p>
							</label>
						</div>
					{/each}
				</div>
			</div>
			<div class="label-select" style="margin-top: 3rem;">
				<Star {isIndicatorActive} {style} bind:rating />
			</div>
			<button type="submit" id="submitButton" disabled={isLoading}>
				{#if firstTime}
					Submit
				{:else}
					Update
				{/if}
			</button>
		</form>
	</div>
	{#if status}
		<div class="status">{status}</div>
	{/if}
{/if}

<style>
	.status {
		width: 100%;
		text-align: center;
		font-size: 1.5rem;
	}
	.form-container {
		display: flex;
		flex-direction: column;
		padding: 1rem;
		border-radius: 1rem;
	}
	form {
		display: flex;
		flex-direction: column;
		justify-items: center;
	}
	.grid-container {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		grid-template-rows: repeat(2, 1fr);
		align-items: center;
		justify-items: center;
		width: 70%;
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
		justify-items: center;
		margin-top: 0.5rem;
	}
	label > input {
		/* Menyembunyikan radio button */
		visibility: hidden;
		position: absolute;
	}
	label:hover {
		cursor: pointer;
	}
	label > input:checked {
		font-size: x-small;
	}
	label > p {
		margin: 0;
		font-weight: 400;
	}
	label > h3 {
		font-size: x-large;
		margin-top: 0;
	}
	#submitButton {
		margin-top: 1rem;
		width: 30%;
		align-self: center;
		border: none;
		outline: none;
		padding: 0.5rem;
		border-radius: 0.7rem;
		font-weight: bold;
		font-size: medium;
		background-color: white;
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
	.comStyle-label {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		margin: 0;
		width: 100%;
		height: 100%;
	}
	.label-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		aspect-ratio: 1/1;
		width: 100%;
		justify-content: center;
	}
	#analytical-bg {
		background: linear-gradient(
			142.58deg,
			rgba(249, 35, 35, 0.916667) -58.63%,
			rgba(217, 217, 217, 0) 92.52%,
			rgba(255, 0, 0, 0) 152.72%,
			rgba(226, 216, 216, 0) 157.79%
		);
		border-radius: 36px 0 0 0;
	}
	#analytical-bg:hover {
		background-color: rgba(1, 166, 240, 0.2);
	}
	#amiable-bg {
		background: linear-gradient(
			50.24deg,
			rgba(65, 249, 35, 0.916667) -51.24%,
			rgba(228, 216, 216, 0) 96.82%,
			rgba(226, 216, 216, 0) 154.41%,
			rgba(217, 217, 217, 0) 159.26%
		);
		border-radius: 0 0 0 36px;
	}
	#amiable-bg:hover {
		background-color: rgba(1, 166, 240, 0.2);
	}
	#driver-bg {
		background: linear-gradient(
			223.79deg,
			rgba(255, 186, 1, 0.916667) -58.63%,
			rgba(228, 216, 216, 0) 92.52%,
			rgba(226, 216, 216, 0) 152.72%,
			rgba(217, 217, 217, 0) 157.79%
		);
		border-radius: 0 36px 0 0;
	}
	#driver-bg:hover {
		background-color: rgba(1, 166, 240, 0.2);
	}
	#expressive-bg {
		background: linear-gradient(
			318.52deg,
			rgba(1, 166, 240, 0.916667) -58.63%,
			rgba(228, 216, 216, 0) 92.52%,
			rgba(226, 216, 216, 0) 152.72%,
			rgba(217, 217, 217, 0) 157.79%
		);
		border-radius: 0 0 36px 0;
	}
	#expressive-bg:hover {
		background-color: rgba(1, 166, 240, 0.2);
	}
</style>
