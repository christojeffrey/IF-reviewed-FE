<script lang="ts">
	import NavBar from './components/navBar.svelte';
</script>

<div class="layout-container">
	<div class="layout-wrapper">
		<NavBar />
		<div class="layout-slot">
			<slot />
		</div>
	</div>
</div>

<style global>
	.layout-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		min-height: 100%;
		min-width: 100%;
		position: relative;
		background-color: #edf0f3;
	}
	.layout-wrapper {
		display: flex;
		flex-direction: column;
		align-items: center;
		background-color: #edf0f3;
		width: 100%;
		max-width: 700px;
		min-height: 100vh;
	}
	.layout-slot {
		width: 95%;
	}
</style>
