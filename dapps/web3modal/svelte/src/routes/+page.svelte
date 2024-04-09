<script lang="ts">
	import { account } from '$lib/web3modal'
	import { Toaster } from 'svelte-french-toast'
	import Network from '../partials/Network.svelte'
	import SignMessage from '../partials/SignMessage.svelte'
	import SignTypeData from '../partials/SignTypeData.svelte'
	import Transaction from '../partials/Transaction.svelte'
	import Wallet from '../partials/Wallet.svelte'
	import CustomForm from '../partials/CustomForm.svelte'

	import { abi as ERC20_ABI } from '$lib/../abi/ERC20/ERC20.json'
	import { readContract, readContracts } from '@wagmi/core'
	import { config } from '$lib/config'

	const tokenQueryAbi = {
		address: '0xFa7e2c67C74EC8D3d90c8a74E1C18A8052Bd0Afa', //sepolia erc-20
		abi: ERC20_ABI,
	} as const

	account.subscribe(async () => {
		console.log(`+Page - new account ${$account.address}`)
		if ($account.chainId === 11155111) {
			console.log(`+Page - chain is sepolia`)
			const [fungibleName, fungibleTicker, fungibleDecimals] =
				await readContracts(config, {
					contracts: [
						{
							...tokenQueryAbi,
							functionName: 'name',
						},
						{
							...tokenQueryAbi,
							functionName: 'symbol',
						},
						{
							...tokenQueryAbi,
							functionName: 'decimals',
						},
					],
				})
			console.log(
				`+Page - Fungible: ${fungibleName.result}, ${fungibleDecimals.result} ${fungibleTicker.result}`,
			)
		} else console.log(`+Page - chain is NOT sepolia`)
	})
</script>

<div class="main">
	<Network />
	<Wallet />
	{#if $account.isConnected}
		<SignMessage />
		<SignTypeData />
		<Transaction />
	{:else}
		<CustomForm />
	{/if}
</div>

<Toaster />

<style>
	.main {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-wrap: wrap;
		gap: 30px;
		width: 100%;
		padding: 20px;
	}
</style>
