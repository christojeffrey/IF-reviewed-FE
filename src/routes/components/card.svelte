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
	<a href={`/detail/${detail.NIM}`}>
		<h1>NIM</h1>
		<p>{detail.NIM}</p>
	</a>
	<!-- reviewStar -->
	<div>
		<Star rating={detail.rating} />
	</div>

	<button on:click|once={reviewButtonOnClick}>review!</button>
</div>

<style>
	a {
		text-decoration: none;
		color: black;
	}
	.card-container {
		background-color: rgb(204, 190, 190);
		border-radius: 0.5rem;
		box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
		padding: 1rem;
		width: fit-content;
	}
	button {
		background-color: rgb(204, 190, 190);
		border-radius: 0.5rem;
		box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
		padding: 1rem;
		width: fit-content;
	}
</style>
