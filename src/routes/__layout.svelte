<script context="module">
	import '../app.postcss';
	import { goto } from '$app/navigation';
	import { crossfade } from 'svelte/transition';
	import { quintOut } from 'svelte/easing';
	import { flip } from 'svelte/animate';

	/**
	 * @typedef {{
	 *   id: number;
	 *   title: string;
	 *   year: number;
	 *   creator: string[];
	 *   rating: string;
	 *   genre: string[];
	 *   runtime_in_minutes: string;
	 *   episodes: number;
	 *   image: string;
	 * }} Cartoon
	 **/
	/** @type {import('@sveltejs/kit').Load} */
	export async function load({ fetch }) {
		const res = await fetch('https://api.sampleapis.com/cartoons/cartoons2D');
		/** @type {Cartoon[]} */
		const cartoons = await res.json();
		cartoons[2].image =
			'https://m.media-amazon.com/images/M/MV5BODBkZWI3MmItMWU2ZC00M2FhLTk1YWQtNjU5YzI0NzI0NmIxXkEyXkFqcGdeQXVyNzA0MTM4NjM@._V1_FMjpg_UY720_.jpg';
		cartoons[21].image =
			'https://m.media-amazon.com/images/M/MV5BMmZjMjU1YmMtY2QwOC00ZjI1LWJlMjEtYTYxOWU1ZGIzOGUwXkEyXkFqcGdeQXVyMTA0MTM5NjI2._V1_FMjpg_UX780_.jpg';

		return {
			props: {
				cartoons
			},
			context: {
				cartoons
			}
		};
	}
</script>

<script>
	/** @type {Cartoon[]} */
	export let cartoons;

	let search = '';

	const [send, receive] = crossfade({
		duration: (d) => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: (t) => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});

	function filter(searchTerm = '') {
		return cartoons.filter((cartoon) => {
			if (searchTerm === '') {
				return true;
			}
			if (cartoon.title.toLowerCase().includes(searchTerm)) {
				return true;
			}
			if (`${cartoon.year}`.includes(searchTerm)) {
				return true;
			}
			return false;
		});
	}

	$: filteredList = filter(search.toLowerCase());
	$: console.log(search);
</script>

<div class="container mx-auto py-8 text-center text-gray-50">
	<label>
		<h1 class="text-6xl py-6">Search</h1>
		<input
			class="px-4 py-2 text-3xl text-center outline-none bg-transparent border-b-4 border-gray-50 rounded-none"
			type="text"
			bind:value={search}
		/>
	</label>
</div>

<div class="container mx-auto grid grid-cols-5 gap-8 py-8">
	{#each filteredList as cartoon (cartoon.id)}
		<a
			href="/cartoon/{cartoon.id}"
			sveltekit:noscroll
			class="flex flex-col items-center bg-gray-200 p-4 rounded-lg transition scale-95 hover:scale-100"
			in:receive={{ key: cartoon.id, duration: 500 }}
			out:send={{ key: cartoon.id, duration: 500 }}
			animate:flip={{
				easing: quintOut,
				duration: 500
			}}
		>
			<img class="h-72 rounded" alt={cartoon.title} src={cartoon.image} />
			<h1 class="text-2xl text-center text-gray-900">{cartoon.title}</h1>
			<h1 class="text-xl text-center text-gray-600">{cartoon.year}</h1>
		</a>
	{/each}
</div>
<div
	on:click={() => {
		goto('/', {
			noscroll: true
		});
	}}
	class="empty:hidden modal fixed inset-0 bg-gray-900 bg-opacity-50 flex justify-center items-center"
>
	<slot />
</div>
