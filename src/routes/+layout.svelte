<script lang="ts">
	import NavBar from './components/navBar.svelte';
</script>

<div class="container">
	<div class="wrapper">
		<NavBar />
		<div class="slot">
			<slot />
		</div>
	</div>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
		min-height: 100%;
	}
	.wrapper {
		display: flex;
		flex-direction: column;
		align-items: center;
		background-color: aliceblue;
		width: 100%;
		max-width: 700px;
		padding: 16px;
		min-height: 100vh;
	}
	.slot {
		margin: 16px;
		width: 100%;
	}
</style>
