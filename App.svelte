<script>
	import { tweened } from "svelte/motion";
	import * as svelteEasings from "svelte/easing";
	import Reaction from "./Reaction.svelte";
	
	const linear = t => t;
	const easings = [linear, ...Object.values(svelteEasings)];

	let emojis = ["💀", "🤓", "🇱", "🍕"]
			.map(emoji => [emoji, tweened(Math.round(Math.random() * 42069))]),
			speed = 150,
	
			easing = svelteEasings.cubicInOut,
			overflow = false,
			addAmount = 10,
			incrementBy = 1;
</script>

<div class="flex">
	{#each emojis as [emoji, count] (emoji)}
		<Reaction {count} {speed} {easing} {overflow}>
			<!-- you should use a twemoji <img> -->
			<svelte:fragment slot="emoji">{emoji}</svelte:fragment>
		</Reaction>
	{/each}
</div>
<fieldset>
	<legend>Controls</legend>
	<label>
		Show overflow:
		<input type="checkbox" bind:checked={overflow} />
	</label>
	<label>
		Easing:
		<select bind:value={easing}>
			{#each easings as _easing}
			<option value={_easing}>{_easing.name}</option>
			{/each}
		</select>
	</label>
	<label>
		Speed:
		<input type="number" bind:value={speed} />
		ms
	</label>
</fieldset>
<fieldset>
	<legend>Set a bunch of reactions at once</legend>
	<div class="flex">
		<select bind:value={incrementBy}>
			<option value={1}>Add</option>
			<option value={-1}>Remove</option>
		</select>
		<input bind:value={addAmount} type="number" />
		<button on:click={() => {
			for (let [,store] of emojis) store.update(c => c + incrementBy * addAmount, { duration: speed });
		}}>Go!</button>
	</div>
</fieldset>

{#if overflow}
	<style>
		.reaction__count-container {
			overflow-y: visible !important;
		}
	</style>
{/if}

<style>
	/* check Reaction.svelte for the actual code */
	
	/* just to look good */
	:global(body) {
		--dark: #36393f;
		--light: #dcddde;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		gap: 1.5rem;
		min-height: 100vh;
		color: var(--light);
		background: var(--dark);
	}
	
	fieldset {
		padding: 1ch 2ch;
	}
	
	.flex {
		display: flex;
		gap: 2ch;
		align-items: center;
		justify-content: center;
	}
	
	/* css resets */
	:global(*, *::before, *::after) {
		box-sizing: border-box;
		margin: 0;
		padding: 0;
	}
	
	:global(button) {
    display: inline-block;
    cursor: pointer;
    text-align: center;
		color: var(--dark);
    -webkit-appearance: none;
    -moz-appearance: none;
	}
	
	:global(input) {
		max-width: 5rem;
	}
</style>
