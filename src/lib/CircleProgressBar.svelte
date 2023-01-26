<script lang="ts">
	export let ratio;

	const angle = 360 * ratio;
	const background = `radial-gradient(white 50%, transparent 51%),
    conic-gradient(transparent 0deg ${angle}deg, gainsboro ${angle}deg 360deg),
    conic-gradient(orange 0deg, yellow 90deg, lightgreen 180deg, green);`;
	
	$: style = `--background:${background}`
</script>

<div class="circle" style="{style}"></div>

<style lang="scss">
	.circle {
		background: var(--background);
		border-radius: 50%;
		width: 11.375rem;
		height: 11.375rem;
	}
</style>
