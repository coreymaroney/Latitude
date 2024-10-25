<script lang="ts">
	import { fade } from 'svelte/transition';
	import comments from '../data/comments.json';

	let video, overlay;
	let time = $state(0);
	let paused = $state(true);

	let showComment = $state(true);
	let showCommentTimeout: number;

	let cursorData = $state({
		clickCount: 0,
		clickLocation: [{ click: 0, x: null, y: null }]
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

	function togglePaused() {
		paused = !paused;
		console.log(time);
	}

	/*
	
	*/
	let videoComments = comments.sort((a, b) => {
		return a.timestamp - b.timestamp;
	});

	const handleComments = (e) => {
		clearTimeout(showCommentTimeout);

		showCommentTimeout = setTimeout(() => (showComment = false), 2500);

		console.log(Math.floor(time), Math.floor(videoComments[0].timestamp));

		var show = Math.floor(time) === Math.floor(videoComments[0].timestamp) ? true : false;
		overlay.style.display = show ? 'block' : 'none';

		if (show) videoComments.splice(0, 1);
		console.log(videoComments);
	};
</script>

<!-- 
1. Display a video player

2. Add a single button outside of the video player to play/pause the video.  

As the video status changes, the button label and functionality should also change ("Play" vs "Pause")

3. Add interactivity to the video / reactivity to the page by completing one of these:
3a. Create a list of 5-10 demo comments that are tied to specific times in the video and display them in sync with video playback.  
You can display them on the video, outside of the video - the layout/style here is completely up to you.  
This should in some way demonstrate data context - through visibility changes, animation or otherwise - that the comments are 'tied' to specific moments.  
Remember to account for users manually scrubbing around the video and possibly restarting playback after a video ends.

3b. React to clicks on the video itself.  
When a user clicks on the video, record the current video time & x/y position of that click (in relation to the video, not page).  
Display the collection of clicks somewhere and update it as new clicks are recorded (a simple reactive table or list is fine). 

3c. Create your own unique feature that shows off interacting with the video in some way through your chosen player's API and/or a visual component that reacts to video interactions. 
You might show a nicely designed log of play/stop/scrubbing actions, use a viz library like Charts.js to show engagement (how much of the video was watched), 
have an external button to rate moments and display aggregate stats (average, total ratings) about those behaviors.

-->

<div class="mx-32 mt-32">
	<video
		id="player"
		playsinline
		onmousemove={handleMouseMove}
		onmousedown={handleMouseDown}
		ontimeupdate={handleComments}
		bind:currentTime={time}
		bind:paused
		bind:this={video}
	>
		<source src="/f1.webm" type="video/webm" />
		<track kind="captions" />
	</video>

	<div id="overlay" bind:this={overlay}>Test</div>

	<div>
		<button onclick={togglePaused}>
			{#key paused}
				<div in:fade>{paused ? 'play' : 'pause'}</div>
			{/key}
		</button>
	</div>

	<div>
		<table class="table-auto border-separate border-spacing-2 border border-slate-500">
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
					<tr>
						{#each Object.values(row) as cell}
							<td>{cell}</td>
						{/each}
					</tr>
				{/each}
			</tbody>
		</table>
	</div>
</div>

<style>
	#video {
		z-index: 1;
	}

	#overlay {
		position: absolute;
		top: 400px;
		font-size: 26px;
		text-align: center;
		color: #ffffffe6;
		width: 90%;
		padding: 12px 0;
		background-color: #c5c5c558;
		z-index: 2;
		display: none;
	}
</style>
