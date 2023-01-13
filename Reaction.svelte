<script>
	export let count, easing, speed;

	let reacted = !Math.round(Math.random());
	
	$: ([ upperCount, lowerCount ] = [ Math.ceil($count), Math.floor($count) ]);
	$: (decimal = $count % 1);
	$: ([ upperOffset, lowerOffset ] = [ decimal - 1, decimal ].map(o =>  easing(o)));
</script>
<button on:click={() => {
	reacted = !reacted;
	count.update(c => c + (+reacted || -1), { duration: speed });
}} class="reaction" class:reaction--reacted={reacted}>
	<span class="reaction__emoji"><slot name="emoji" /></span>
	<span class="reaction__count-container">
		{#each [
			["lower", lowerCount, lowerOffset],
			["upper", upperCount, upperOffset]
		] as [type, count, offset] (type)}
		<span class="reaction__count reaction__count--{type}" style:--offset={offset}>{count}</span>
		{/each}
	</span>
</button>
<style>
	.reaction {
		--height: 1.5rem;
		--border-width: 1px;
		--border-color: transparent;
		--background-color: #2f3136;
		color: var(--light);
		font-size: 0.75rem;
		
		display: flex;
		align-items: center;
		justify-content: center;

		height: var(--height);
		max-height: var(--height);
		padding: 0 1ch;
		margin: 0;
		gap: 0.5ch;

		border-color: var(--border-color);
		border-width: var(--border-width);
		border-radius: 0.5em;
		transition-property: border, background-color;
		transition-duration: 0.25s;
	}

	/* for some reason when i click on the reaction */
	/* in firefox there's a flash of white background */
	/* hence the :active */
	.reaction, .reaction:active {
		background-color: var(--background-color);
	}
	
	.reaction:not(.reaction--reacted):where(:focus-visible, :hover) {
		--border-color: #5e6165;
		--background-color: #36393f;
	}
	
	.reaction--reacted {
		--border-color: #5865f2;
		--background-color: #3b405a;
	}

	.reaction__count-container {
		display: grid;
		grid-template-columns: 1fr;
		place-items: center;
		overflow-y: clip;
	}
	
	.reaction__count {
		grid-area: 1 / 1;
		transform: translateY(calc(var(--offset) * var(--height) * -1));
	}
	
	.reaction__emoji, .reaction__count {
		display: flex;
		align-items: center;
		justify-content: center;
	}
	
	.reaction__emoji, .reaction__count-container {
		padding-block: 0.5ch;
	}
</style>
