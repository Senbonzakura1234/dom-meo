<script lang="ts">
	import image02 from '$lib/assets/images/image06.jpg';
	import image04 from '$lib/assets/images/image07.jpg';
	import image05 from '$lib/assets/images/image10.jpg';
	import image03 from '$lib/assets/images/image12.jpg';
	import image01 from '$lib/assets/images/image13.jpg';

	const images = [image02, image04, image05, image03, image01];

	let handleOnDown = (e: MouseEvent | Touch | undefined) => {
		if (!document) return;

		const track = document.getElementById('image-track');

		if (!track) return;
		if (!e) return;
		return (track.dataset.mouseDownAt = `${e.clientX}`);
	};

	let handleOnUp = () => {
		if (!document) return;

		const track = document.getElementById('image-track');

		if (!track) return;
		track.dataset.mouseDownAt = '0';
		track.dataset.prevPercentage = track.dataset.percentage;
	};

	let handleOnMove = (e: MouseEvent | Touch | undefined) => {
		if (!document) return;

		const track = document.getElementById('image-track');
		const wrapper = document.getElementById('wrapper');

		if (!track || !wrapper) return;
		if (!track.dataset.mouseDownAt || track.dataset.mouseDownAt === '0' || !e) return;

		const mouseDelta = parseFloat(track.dataset.mouseDownAt) - e.clientX,
			maxDelta = wrapper.clientWidth / 2;

		const percentage = (mouseDelta / maxDelta) * -100,
			nextPercentageUnconstrained = parseFloat(track.dataset.prevPercentage || '') + percentage,
			nextPercentage = Math.max(Math.min(nextPercentageUnconstrained, 0), -100);

		track.dataset.percentage = `${nextPercentage}`;

		track.animate({ transform: `translate(${nextPercentage}%, -50%)` }, { duration: 1200, fill: 'forwards' });

		for (const image of track.getElementsByClassName('image')) {
			image.animate({ objectPosition: `${100 + nextPercentage}% center` }, { duration: 1200, fill: 'forwards' });
		}
	};
</script>

<!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
<div
	class="h-full w-full relative"
	on:mousedown={handleOnDown}
	on:touchstart={e => handleOnDown(e.touches[0])}
	on:mouseup={handleOnUp}
	on:touchend={handleOnUp}
	on:mousemove={handleOnMove}
	on:touchmove={e => handleOnMove(e.touches[0])}
	role="main"
	id="wrapper"
>
	<div class="prose w-full max-w-none text-center text-3xl py-10">
		<h1 class="text-base-100">Đốm Meo</h1>
	</div>

	<div
		id="image-track"
		class="w-full prose flex gap-[4vmin] absolute inset-y-1/2 left-1/4 -translate-y-1/2 h-1/2"
		data-mouse-down-at="0"
		data-prev-percentage="0"
		role="list"
	>
		{#each images as image}
			<img
				class="image my-auto object-cover w-[30vmin] h-full"
				style="object-position: 100% center"
				src={image}
				draggable="false"
				alt="alt"
			/>
		{/each}
	</div>
</div>
