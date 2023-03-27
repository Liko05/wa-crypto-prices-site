<script>
	import { onMount, onDestroy } from 'svelte';
	export let cryptoObject;

	let loading = false;
	let refreshInterval = setInterval(refresh, 60000);
	let timestamp = new Date().toLocaleString();
	let priceString;

	let currencySelect;

	onMount(() => {
		refresh();
	});

	onDestroy(() => {
		clearInterval(refreshInterval);
	});

	function refresh() {
		loading = true;
		fetch(`https://api.coincap.io/v2/assets/${cryptoObject.id}`)
			.then((response) => response.json())
			.then((data) => {
				cryptoObject = data.data;
				timestamp = new Date().toLocaleString();
				priceString = Number(cryptoObject.priceUsd).toFixed(2) + ' USD';
				setTimeout(() => {
					loading = false;
				}, 1000);
			});
	}

	async function changeCurrency() {
		let currency = currencySelect.value;
		let exchangeRates = await fetch(
			`https://cdn.jsdelivr.net/gh/fawazahmed0/currency-api@1/latest/currencies/usd.json`
		).then((response) => response.json());
		let exchangeRate = exchangeRates.usd[currency];
		let price = Number(cryptoObject.priceUsd) * Number(exchangeRate);
		priceString = price.toFixed(2) + ' ' + currency.toUpperCase();
	}
</script>

<div class="flex gap-5 p-5 m-5 border-black border-2">
	{#if loading}
		<div class="flex gap-5 p-5 m-5 border-black border-2">
			<div class="animate-spin rounded-full h-32 w-32 border-b-2 border-gray-900" />
		</div>
	{:else}
		<h1 class="text-3xl">{cryptoObject.name}</h1>
		<h3 class="text-3xl">{priceString}</h3>
		<h6 class="text-sm">Last updated: {timestamp}</h6>
		<select on:change={changeCurrency} bind:this={currencySelect}>
			<option selected="selected" value="usd">USD</option>
			<option value="eur">EUR</option>
			<option value="czk">CZK</option>
		</select>
		<button
			on:click={refresh}
			class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
		>
			Refresh
		</button>
	{/if}
</div>
