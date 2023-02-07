<script lang="ts">
	import { goto } from '$app/navigation';
	import Star from './star.svelte';
	export let detail: any;
	function reviewButtonOnClick() {
		console.log('review');
		goto(`/form/${detail.NIM}`);
	}
</script>

<div class="card-container">
	<div class="card">
		<a class="detail" href={`/detail/${detail.NIM}`}>
			<div class="user-name">{detail.name}</div>
			<div class="user-nim">{detail.NIM}</div>
			<!-- reviewStar -->
			<div>
				<Star rating={detail.rating} />
			</div>
		</a>

		<button on:click|once={reviewButtonOnClick}>review!</button>
	</div>
</div>

<style>
	.detail {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
		height: 100%;
		padding: 4px;
	}
	.user-name {
		font-size: 1.5rem;
		font-weight: bold;
	}
	.user-nim {
		padding: 2px;
	}
	a {
		text-decoration: none;
		color: black;
	}
	.card-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
		height: 100%;
		padding: 8px;
	}
	.card {
		background-color: white;
		border-radius: 0.5rem;
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
		height: 100%;
	}
	button {
		margin: 8px;
		color: #fff;
		background-color: #38c172;
		border-radius: 1.5rem;
		padding: 1rem;
		width: fit-content;
		border: none;
	}
</style>
