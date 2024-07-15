<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import Play from 'phosphor-svelte/lib/Play';
	import Pause from 'phosphor-svelte/lib/Pause';
	import Stop from 'phosphor-svelte/lib/Stop';

	let hr = 0;
	let min = 0;
	let sec = 0;
	let timer: string | number | NodeJS.Timeout | undefined;
	let running = false;

	function handlePlay() {
		if (running) stopTimer();
		else startTimer();
	}

	function startTimer() {
		if (running) return;
		running = true;
		timer = setInterval(() => {
			sec++;
			if (sec === 60) {
				sec = 0;
				min++;
			}
			if (min === 60) {
				min = 0;
				hr++;
			}
		}, 1000);
	}

	function stopTimer() {
		clearInterval(timer);
		running = false;
	}

	function resetTimer() {
		clearInterval(timer);
		hr = 0;
		min = 0;
		sec = 0;
		running = false;
	}

	function padNumber(num: number) {
		return num.toString().padStart(2, '0');
	}

	function handleKeydown(event: KeyboardEvent) {
		switch (event.key) {
			case 's':
				startTimer();
				break;
			case 'p':
				stopTimer();
				break;
			case 'r':
				resetTimer();
				break;
		}
	}

	onMount(() => {
		if (typeof window !== 'undefined') {
			window.addEventListener('keydown', handleKeydown);
		}
		return () => {
			if (typeof window !== 'undefined') {
				window.removeEventListener('keydown', handleKeydown);
			}
		};
	});
</script>

<main>
	<div class="mainwin flex flex-col justify-center items-center gap-24">
		<div class="timer">
			<h1>{padNumber(hr)}:{padNumber(min)}:{padNumber(sec)}</h1>
		</div>
		<div class="controls flex flex-row gap-5">
			<button class="btn btn-xl variant-ghost-primary" on:click={handlePlay}>
				{#if running}
					<Pause size={32} />
				{:else}
					<Play size={32} />
				{/if}
			</button>
			<button class="btn btn-xl variant-ghost-primary" on:click={resetTimer}>
				<Stop size={32} />
			</button>
		</div>
	</div>
</main>

<style>
	.mainwin {
		text-align: center;
		font-family: Arial, sans-serif;
		height: 100vh;
		width: 100vw;
	}
	button {
		margin: 5px;
	}

	.timer {
		font-size: 10vi;
	}
</style>
