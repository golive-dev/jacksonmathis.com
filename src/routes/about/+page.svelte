<script lang="ts">
	let age = $state(14);
	let showFact = $state(false);
	let factIndex = $state(0);

	const facts = [
		'I just started learning to code.',
		'My dad builds software for a living.',
		'I once ate an entire pizza by myself. No regrets.',
		'Football is life.',
		'I think the terminal is cooler than any app.',
		'This website is my first real project.',
	];

	function nextFact() {
		factIndex = (factIndex + 1) % facts.length;
		showFact = true;
	}
</script>

<div class="min-h-screen px-6 py-20 max-w-2xl mx-auto">
	<a href="/" class="font-mono text-xs text-gray-600 hover:text-neon-green transition-colors">← back</a>

	<h1 class="font-[family-name:var(--font-family-display)] text-4xl md:text-5xl font-bold mt-8 mb-2">
		<span class="text-neon-pink glow-pink">/about</span>
	</h1>

	<div class="mt-12 space-y-8">
		<div class="bg-dark-card border border-dark-border rounded-lg p-6">
			<h2 class="font-mono text-neon-green text-sm mb-4">// basics</h2>
			<div class="grid grid-cols-2 gap-4 font-mono text-sm">
				<div>
					<p class="text-gray-500">name</p>
					<p class="text-white">Jackson Mathis</p>
				</div>
				<div>
					<p class="text-gray-500">age</p>
					<p class="text-white">{age}</p>
				</div>
				<div>
					<p class="text-gray-500">status</p>
					<p class="text-neon-green">learning to code</p>
				</div>
				<div>
					<p class="text-gray-500">vibe</p>
					<p class="text-neon-blue">curious</p>
				</div>
			</div>
		</div>

		<div class="bg-dark-card border border-dark-border rounded-lg p-6">
			<h2 class="font-mono text-neon-blue text-sm mb-4">// random fact generator</h2>
			<button
				onclick={nextFact}
				class="font-mono text-sm border border-neon-pink/30 text-neon-pink px-4 py-2 rounded-lg
					   hover:bg-neon-pink/10 hover:border-neon-pink/60 transition-all duration-200 active:scale-95 cursor-pointer"
			>
				tell me something
			</button>
			{#if showFact}
				<p class="mt-4 text-gray-300 font-mono text-sm animate-[pulse-glow_2s_ease-in-out_1]">
					→ {facts[factIndex]}
				</p>
			{/if}
		</div>

		<div class="bg-dark-card border border-dark-border rounded-lg p-6">
			<h2 class="font-mono text-neon-purple text-sm mb-4">// currently into</h2>
			<div class="flex flex-wrap gap-2">
				{#each ['football', 'web dev', 'music', 'gaming', 'airplanes', 'the internet', 'building stuff'] as thing}
					<span class="font-mono text-xs border border-dark-border text-gray-400 px-3 py-1 rounded-full hover:text-neon-purple hover:border-neon-purple/30 transition-all duration-200">
						{thing}
					</span>
				{/each}
			</div>
		</div>
	</div>
</div>
