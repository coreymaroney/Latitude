<script lang="ts">
	import { fade } from 'svelte/transition';
	import Logo from '../lib/logo.svelte';
	import Play from '../lib/play.svelte';
	import Pause from '../lib/pause.svelte';

	let video;
	let time = $state(0);
	let duration = $state(0);
	let paused = $state(true);

	let cursorData = $state({
		clickCount: 0,
		clickLocation: [{ click: null, x: null, y: null }]
	});
	let cursorLocation = $state({ x: 0, y: 0 });

	const formatTime = (timestamp: number) => {
		const minutes = Math.floor(timestamp / 60);
		timestamp = Math.floor(timestamp % 60);
		if (timestamp < 10) timestamp = '0' + timestamp;

		return `${minutes}:${timestamp}`;
	};

	const handleMouseMove = (e) => {
		cursorLocation.x = e.clientX - 128;
		cursorLocation.y = e.clientY - 128;
	};

	const handleMouseDown = (e) => {
		cursorData.clickCount++;
		cursorData.clickLocation.push({
			click: cursorData.clickCount,
			time: formatTime(time),
			x: cursorLocation.x,
			y: cursorLocation.y < 0 ? 0 : cursorLocation.y
		});
		console.log($state.snapshot(cursorData));
	};

	const togglePaused = () => {
		paused = !paused;
	};
</script>

<div class="ml-12 mt-12">
	<Logo />
</div>

<div class="mx-32 mt-12">
	<div>
		<video
			class="rounded-2xl"
			id="player"
			playsinline
			onmousemove={handleMouseMove}
			onmousedown={handleMouseDown}
			bind:this={video}
			bind:currentTime={time}
			bind:duration
			bind:paused
		>
			<source src="/f1.webm" type="video/webm" />
			<track kind="captions" />
		</video>
	</div>

	<div class="flex flex-row pt-2 pb-4">
		<button
			onclick={togglePaused}
			class="w-24 rounded inline-flex items-center align-middle p-2 text-white rounded-xl transition duration-200 ease-out opacity-75 hover:opacity-100 bg-gradient-to-r from-[#e62927] to-[#ba0086] hover:from-[#d2023d] hover:to-[#8510a0]"
		>
			{#key paused}
				{#if paused}
					<Play /> <span class="pl-1.5"> Play </span>
				{:else}
					<Pause /> <span class="pl-1.5"> Pause </span>
				{/if}
			{/key}
		</button>
		<span class="text-white content-center pl-2">{formatTime(time)} / {formatTime(duration)}</span>
	</div>

	<div class="">
		<table class="text-white table-auto border-separate border-spacing-2 border border-slate-500">
			<thead>
				<tr>
					<th>Click</th>
					<th>Timestamp</th>
					<th>X</th>
					<th>Y</th>
				</tr>
			</thead>
			<tbody>
				{#each Object.values(cursorData.clickLocation) as row}
					<tr in:fade>
						{#each Object.values(row) as cell}
							<td>{cell}</td>
						{/each}
					</tr>
				{/each}
			</tbody>
		</table>
	</div>
</div>
