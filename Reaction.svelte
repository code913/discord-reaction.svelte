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
		--height: 2rem;
		--border-width: 0.125em;
		background-color: var(--dark);
		color: var(--light);
		
		display: flex;
		align-items: center;
		justify-content: center;

		height: var(--height);
		max-height: var(--height);
		padding: 0 1ch;
		margin: 0;
		gap: 0.5ch;

		border-color: var(--light);
		border-width: var(--border-width);
		border-radius: 0.5em;
		transition: border 0.25s;
	}
	
	/* for some reason when i click on the reaction */
	/* in firefox there's a flash of white background */
	.reaction:focus, .reaction:hover {
		background-color: var(--dark);
		/* set your own */
		outline: revert;
	}
	
	.reaction--reacted {
		border-color: #5865f2;
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
