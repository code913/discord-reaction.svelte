<script>
	import { tweened } from "svelte/motion";
	import * as svelteEasings from "svelte/easing";
	import { onMount } from "svelte";
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
	
	onMount(() => {
		for (let [, store] of emojis) {
			setTimeout(() => store.update(c => c + Math.round(Math.random() * 4) - 2), Math.round(Math.random() * 2500));
		}
	});
</script>

<article class="message">
	<p>code913: svelte is a really nice framework</p>
	<ul class="reactions">
		{#each emojis as [emoji, count] (emoji)}
			<li>
				<Reaction {count} {speed} {easing}>
					<!-- you should use a twemoji <img> -->
					<svelte:fragment slot="emoji">{emoji}</svelte:fragment>
				</Reaction>
			</li>
		{/each}
	</ul>
</article>
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
	:global(html) {
		font-size: min(5vh, 32px);
	}
	
	:global(body) {
		--dark: #36393f;
		--light: #dcddde;
		display: flex;
		flex-direction: column;
		align-items: center;
		text-align: center;
		padding-block: 2.5rem;
		gap: 1.5rem;
		min-height: 100vh;
		overflow: auto;
		color: var(--light);
		background: var(--dark);
	}
	
	fieldset {
		padding: 1ch 2ch;
	}
	
	.message {
		text-align: start;
	}
	
	.reactions {
		display: flex;
		margin-top: 0.5ch;
		gap: 0.5ch;
		align-items: center;
		justify-content: start;
		list-style-type: none;
	}
	
	:global(button):where(:focus, :focus-within, :focus-within) {
		outline: revert;
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
