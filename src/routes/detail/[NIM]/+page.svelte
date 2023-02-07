<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	import { BACKEND_BASE_URL } from '../../../lib/constants';
	import Star from '../../components/star.svelte';
	import { COM_STYLES } from '../../../lib/constants';
	const NIM = $page.params.NIM;
	let data: any = {};
	let isLoading: boolean = true;
	let comSyle: String = '-';
	let style = {
		styleStarWidth: 40,
		styleEmptyStarColor: '#737373',
		styleFullStarColor: '#ffd219'
	};
	onMount(async () => {
		isLoading = true;
		const response = await fetch(`${BACKEND_BASE_URL}/user/${NIM}`, {
			method: 'GET',
			headers: {
				'Content-Type': 'application/json'
			}
		});
		const res = await response.json();
		if (res.error) {
			console.log(res.error);
		}
		data = res.data;
		console.log(data);
		isLoading = false;
		comSyle = data.comStyle == 0 ? '-' : COM_STYLES[data.comStyle - 1].text;
	});
</script>

{#if isLoading}
	<h1>Loading...</h1>
{:else if !data}
	<h1>Person Doesn't Exist</h1>
{:else}
	<div class="detail-container">
		<div class="info">
			<h1 id="name">{data.name}</h1>
			<h3 id="comStyle">{comSyle}</h3>
		</div>
		<h2 id="nim">{data.NIM}</h2>
		<div class="review">
			<div class="info-rating">
				<Star rating={data.rating} {style} isIndicatorActive={false} />
				<p>{data.rating}</p>
			</div>
			<p id="totalReview">{data.totalReviews} people reviewed</p>
		</div>
	</div>
{/if}

<style>
	h1,
	h2,
	h3 {
		margin: 0;
	}
	#name {
		margin-bottom: 0;
	}
	#nim {
		margin-top: 0;
		margin-bottom: 0.5rem;
	}
	.detail-container {
		border-radius: 1rem;
		margin-top: 2rem;
		display: flex;
		flex-direction: column;
		background-color: white;
		padding: 1.5rem 1.5rem;
	}
	.info {
		display: grid;
		grid-template-columns: 1fr 1fr;
		align-items: center;
	}
	.info-rating {
		display: flex;
		flex-direction: row;
		align-items: center;
		gap: 0.5rem;
		font-size: x-large;
		font-weight: bold;
	}
	#comStyle {
		justify-self: end;
	}
	#totalReview {
		font-size: x-large;
		font-weight: bold;
		margin: 0;
	}
</style>
