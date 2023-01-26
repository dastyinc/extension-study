<script lang="ts">
	export let ratio;
	export let hours;
	export let minutes;
	export let seconds;
	export let ResetTimer = () => {};

	$: angle = 360 * ratio;
	$: background = `radial-gradient(white 50%, transparent 51%),
    conic-gradient(transparent 0deg ${angle}deg, gainsboro ${angle}deg 360deg), #28222d`;

	$: style = `--background:${background}`
</script>

<div class="circle" style={style}>
	<div class="time">{hours}:{minutes}:{seconds}</div>
	<div class="reset" on:click={ResetTimer}>Reset</div>
</div>

<style lang="scss">
	.circle {
		background: var(--background);
		border-radius: 50%;
		width: 13rem;
		height: 13rem;
		box-shadow: 0 5px 10px 0 rgba(0, 0, 0, 0.3);
	}

	.time{
		color: #28222d;
		font-size: 1.438rem;
		font-weight: bold;
		font-family: NotoSansKR;
		width: fit-content;
		position: relative;
		transform: translate(-50%, -50%);
		top: 6.5rem;
		left: 6.5rem;
	}

	.reset{
		font-family: NotoSansKR;
		font-size: 0.75rem;
		font-weight: 300;
		color: #28222d;
		position: relative;
		width: fit-content;
		transform: translate(-50%, -50%);
		top: 6.5rem;
		left: 6.5rem;
	}
	.reset:hover{
		cursor: pointer;
	}
</style>
