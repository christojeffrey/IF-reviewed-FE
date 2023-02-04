<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	const NIM = $page.params.NIM;
	let data: any = {};
	onMount(async () => {
		const response = await fetch(`http://localhost:3000/api/user/${NIM}`, {
			method: 'GET',
			headers: {
				'Content-Type': 'application/json'
			}
		});
		const res = await response.json();
		if (res.error) {
			console.log(res.error);
			return;
		}
		data = res.data;
		console.log(data);
	});
</script>

<h1>detail</h1>
<h1>{data.NIM}</h1>
<h1>{data.name}</h1>
<h1>{data.rating}</h1>
<h1>{data.totalReviews}</h1>
<h1>{data.comStyle}</h1>
