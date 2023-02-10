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
		styleStarWidth: 60,
		styleEmptyStarColor: '#ffffff',
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
		console.log(selectedComStyles);
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
		<div style="display: grid; justify-items: center;">
			<div class="grid-container">
				<div class="label-container" id="analytical-bg">
					<label class="comStyle-label">
						<input
							type="radio"
							name="comStyles"
							id="amiable"
							value="1"
							on:click={handleComStyleChange}
						/>
						<h3>ANALYTICAL</h3>
						<p>serious</p>
						<p>critical</p>
					</label>
				</div>
				<div class="label-container" id="driver-bg">
					<label class="comStyle-label">
						<input
							type="radio"
							name="comStyles"
							id="driver"
							value="2"
							on:click={handleComStyleChange}
						/>
						<h3>DRIVER</h3>
						<p>dominating</p>
						<p>decisive</p>
					</label>
				</div>
				<div class="label-container" id="amiable-bg">
					<label class="comStyle-label">
						<input
							type="radio"
							name="comStyles"
							id="expressive"
							value="3"
							on:click={handleComStyleChange}
						/>
						<h3>AMIABLE</h3>
						<p>agreeable</p>
						<p>supportive</p>
					</label>
				</div>
				<div class="label-container" id="expressive-bg">
					<label class="comStyle-label">
						<input
							type="radio"
							name="comStyles"
							id="analytical"
							value="4"
							on:click={handleComStyleChange}
						/>
						<h3>EXPRESSIVE</h3>
						<p>ambitious</p>
						<p>enthusiastic</p>
					</label>
				</div>
			</div>
		</div>
		<div class="label-select" style="margin-top: 3rem;">
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
		border: 2px solid #f00;
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

		margin: 0;
	}
	.label-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		aspect-ratio: 1/1;
		width: 100%;
		border: 4px solid #667181;
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
</style>
