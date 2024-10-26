<script lang="ts">
	import { fade } from 'svelte/transition';
	import Logo from '$lib/logo.svelte';
	import Play from '$lib/play.svelte';
	import Pause from '$lib/pause.svelte';

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

	const scrollIntoView = ({ target }) => {
		const row = document.querySelector(target.getAttribute('data-cursor'));
		if (!row) return;
		row.scrollIntoView({
			behavior: 'smooth'
		});
	};

	const handleCursor = (e) => {
		cursorLocation.x = e.clientX - 128;
		cursorLocation.y = e.clientY - 128;
	};

	const handleVideoClick = (e) => {
		cursorData.clickCount++;
		cursorData.clickLocation.push({
			click: cursorData.clickCount,
			time: formatTime(time),
			x: cursorLocation.x,
			y: cursorLocation.y < 0 ? 0 : cursorLocation.y
		});

		scrollIntoView(e);
	};

	const togglePaused = () => {
		paused = !paused;
	};
</script>

<div class="ml-12 mt-6">
	<Logo />
</div>

<div class="mx-32 mt-12 font-mono">
	<div>
		<video
			class="rounded-2xl"
			id="player"
			playsinline
			bind:currentTime={time}
			bind:duration
			bind:paused
			data-cursor="#row"
			onclick={handleVideoClick}
			onmousemove={handleCursor}
		>
			<source src="/f1.webm" type="video/webm" />
			<track kind="captions" />
		</video>
	</div>

	<div class="flex flex-row pt-2 pb-4">
		<button
			onclick={togglePaused}
			class="w-24 rounded-full inline-flex items-center align-middle p-2 text-white rounded-xl transition duration-200 ease-out opacity-75 hover:opacity-100 bg-gradient-to-r from-[#e62927] to-[#ba0086] hover:from-[#d2023d] hover:to-[#8510a0]"
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

	<div class="flex justify-center mb-16">
		{#if cursorData.clickCount}
			<table
				in:fade
				class="w-56 text-white rounded table-auto border-separate border-double border-spacing-5 border border-slate-500"
			>
				<thead>
					<tr class="">
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
								<td id="row">{cell}</td>
							{/each}
						</tr>
					{/each}
				</tbody>
			</table>
		{/if}
	</div>
</div>
