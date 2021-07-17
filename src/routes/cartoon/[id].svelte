<script context="module">
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

	/**
	 * @type {import('@sveltejs/kit').Load<{
	 *   context: {
	 *     cartoons: Cartoon[];
	 *   };
	 *   pageParams: {
	 *     id: string;
	 *   };
	 * }>}
	 **/
	export async function load({ context, page }) {
		const { id: matchingId } = page.params;
		const { cartoons } = context;
		const cartoon = cartoons.find(({ id }) => `${id}` === matchingId);
		if (cartoon) {
			return {
				props: { cartoon }
			};
		}
	}
</script>

<script>
	/** @type {Cartoon} */
	export let cartoon;
</script>

<div class="p-8 rounded-xl shadow-xl bg-gray-50 flex gap-8 w-[48rem] max-w-screen">
	<img src={cartoon.image} alt={cartoon.title} class="w-1/3 rounded-lg" />
	<div class="w-2/3">
		<h1 class="text-4xl">{cartoon.title}</h1>
		<h2 class="text-3xl text-gray-700">{cartoon.year}</h2>
		<p class="text-xl text-gray-700">{cartoon.creator.join(', ')}</p>
		<dl class="text-lg">
			<dt class="text-xl">Genres</dt>
			<dd class="ml-4">{cartoon.genre.join(', ')}</dd>
			<dt class="text-xl">Rating</dt>
			<dd class="ml-4">{cartoon.rating}</dd>
			<dt class="text-xl">Episode Count</dt>
			<dd class="ml-4">{cartoon.episodes}</dd>
		</dl>
	</div>
</div>
