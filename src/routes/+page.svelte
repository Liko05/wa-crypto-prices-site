<script>
	import { onMount } from 'svelte';
	import Crypto from '../components/Crypto.svelte';

	let preflight = false;
	let currencies = [];

	onMount(async () => {
		const response = await fetch('https://api.coincap.io/v2/assets');
		if (response.status === 200) {
			const data = await response.json();
			preflight = true;
			currencies = data.data.slice(0, 10);
			console.log(currencies);
		}
	});
</script>

{#if preflight}
	{#each currencies as currency}
		<Crypto cryptoObject={currency} />
	{/each}
{/if}
