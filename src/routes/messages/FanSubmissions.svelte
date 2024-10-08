<script lang="ts">
	import * as m from '$lib/paraglide/messages';
	import { scrollPos } from '$lib/scrollStore';
	import type { ArtSubmissionData } from '$lib/types/types';
	import AnchorScroll from './AnchorScroll.svelte';
	import SubmissionCard from './SubmissionCard.svelte';
	import Toggler from './Toggler.svelte';
	import { onMount } from 'svelte';

	export let data: ArtSubmissionData[];
	const colors = ['blue', 'yellow', 'pink'] as const;
	const getColor = (index: number) => colors[index % colors.length];
	let sectionRef: HTMLElement;

	onMount(() => {
		const observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						scrollPos.set({ section: 'ms' });
					} else {
						scrollPos.set({ section: 'msFooter' });
					}
				});
			},
			{
				threshold: 0.0001 // Adjust as needed
			}
		);

		if (sectionRef) {
			observer.observe(sectionRef);
		}

		return () => {
			observer.disconnect();
		};
	});
</script>

<section class="group flex flex-col items-center" bind:this={sectionRef}>
	<h1 class="text-3xl font-medium z-10">{m.videoHeader()}</h1>
	<div class="rounded-md w-10/12 my-16 aspect-video overflow-hidden" id="video-container">
		<video class="w-full h-full" src=""></video>
	</div>
	<h1 class="text-3xl z-10 mb-8">{m.messagesHeader()}</h1>
	<div class="flex md:gap-4 gap-2">
		<Toggler label={m.messages()} id="show-messages" />
		<Toggler label={m.artworks()} id="show-artwork" />
		<Toggler label={m.photos()} id="show-pictures" />
		<Toggler label={m.videos()} id="show-videos" />
	</div>
	<!-- Submissions -->
	<div class="flex justify-center py-8 w-10/12">
		<div class="md:columns-2 lg:columns-3 max-w-[96rem]" style="column-gap: 40px;">
			{#each data as item, i}
				<SubmissionCard data={item} color={getColor(i)} />
			{/each}
		</div>
	</div>
</section>

{#if $scrollPos.section === 'msFooter'}
	<AnchorScroll targetElement={sectionRef} section="ms" direction="top"></AnchorScroll>
{/if}

<style>
	#video-container {
		border: 5px solid #ffd6ac;
	}
	h1 {
		color: #2e3191;
	}
</style>
