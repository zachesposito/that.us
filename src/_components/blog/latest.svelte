<script context="module">
	import { getPosts } from '$blog/getPosts';
	import membersQueryApi from '$dataSources/api.that.tech/members/queries';

	// export const prerender = true;
	export async function load({ fetch }) {
		const { queryBlogAuthorBySlug } = membersQueryApi(fetch);
		const rawPosts = getPosts({ page: 1, limit: 3 });

		let posts = await Promise.all(
			rawPosts.map(async (p) => {
				const author = await queryBlogAuthorBySlug(p.metadata.authorSlug);

				return {
					...p,
					metadata: {
						...p.metadata,
						author
					}
				};
			})
		);

		return {
			props: {
				posts
			}
		};
	}
</script>

<script>
	export let posts;

	import { Standard as StandardLink } from '$elements/links';
	import Card from './components/blogCard.svelte';
</script>

<div class="relative py-16 sm:py-24">
	<div class="relative">
		<div class="text-center mx-auto max-w-md px-4 sm:max-w-3xl sm:px-6 lg:px-8 lg:max-w-7xl">
			<h2 class="text-base font-semibold tracking-wider text-thatOrange-400 uppercase">
				THAT BLOG
			</h2>
			<p class="mt-2 text-3xl font-extrabold text-gray-900 tracking-tight sm:text-4xl">
				The Latest Announcemnts, Updates and Words
			</p>
			<p class="my-12 mx-auto max-w-prose text-xl text-gray-500">
				<StandardLink href="/blog/">View All Blog Posts</StandardLink>
			</p>
		</div>
		<div
			class="mt-12 mx-auto max-w-md px-4 grid gap-8 sm:max-w-lg sm:px-6 lg:px-8 lg:grid-cols-3 lg:max-w-7xl"
		>
			{#each posts as post}
				<Card metadata={post.metadata} />
			{/each}
		</div>
	</div>
</div>
