<script lang="ts">
	let showFact = $state(false);
	let factIndex = $state(0);

	const facts = [
		'The Wright brothers\' first flight was shorter than a 737 fuselage.',
		'I once ate an entire pizza by myself. No regrets.',
		'Football is life.',
		'A 747 has about 6 million parts.',
		'My dad builds software for a living.',
		'This website is my first real project.',
		'The speed of sound is about 767 mph.',
		'I want to get my pilot\'s license one day.',
	];

	function nextFact() {
		factIndex = (factIndex + 1) % facts.length;
		showFact = true;
	}

	const stats = [
		{ label: 'NAME', value: 'Jackson Mathis' },
		{ label: 'AGE', value: '14' },
		{ label: 'RANK', value: 'Cadet' },
		{ label: 'STATUS', value: 'Learning to Fly' },
		{ label: 'CLEARANCE', value: 'Student' },
		{ label: 'HOME BASE', value: 'USA' },
	];

	const interests = [
		{ name: 'AVIATION', icon: '✈️' },
		{ name: 'FOOTBALL', icon: '🏈' },
		{ name: 'GAMING', icon: '🎮' },
		{ name: 'MUSIC', icon: '🎵' },
		{ name: 'WEB DEV', icon: '💻' },
		{ name: 'BUILDING', icon: '🔧' },
		{ name: 'THE INTERNET', icon: '🌐' },
	];
</script>

<div class="min-h-screen px-6 py-16 max-w-3xl mx-auto">
	<a href="/" class="text-xs transition-colors duration-200" style="color: var(--av-amber-dim);"
		onmouseenter={(e) => e.currentTarget.style.color = 'var(--av-amber)'}
		onmouseleave={(e) => e.currentTarget.style.color = 'var(--av-amber-dim)'}>
		← RETURN TO COCKPIT
	</a>

	<h1 class="text-4xl md:text-5xl font-bold mt-8 mb-2" style="font-family: 'Orbitron', sans-serif;">
		<span class="glow-amber" style="color: var(--av-amber);">PILOT DOSSIER</span>
	</h1>
	<p class="text-xs mb-12" style="color: var(--av-amber-dim);">CLASSIFIED — AUTHORIZED PERSONNEL ONLY</p>

	<!-- Stats grid -->
	<div class="instrument-panel p-6 mb-6">
		<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">▸ identification</p>
		<div class="grid grid-cols-2 md:grid-cols-3 gap-4">
			{#each stats as stat}
				<div>
					<p class="text-[10px] tracking-widest" style="color: var(--av-amber-dim);">{stat.label}</p>
					<p class="text-sm mt-0.5" style="color: var(--av-green);">{stat.value}</p>
				</div>
			{/each}
		</div>
	</div>

	<!-- Mission briefing (random facts) -->
	<div class="instrument-panel p-6 mb-6">
		<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">▸ mission briefing</p>
		<button
			onclick={nextFact}
			class="text-xs px-4 py-2 rounded transition-all duration-200 cursor-pointer active:scale-95"
			style="border: 1px solid var(--av-amber); color: var(--av-amber); background: transparent;"
			onmouseenter={(e) => e.currentTarget.style.background = 'rgba(255,176,0,0.1)'}
			onmouseleave={(e) => e.currentTarget.style.background = 'transparent'}>
			DECLASSIFY INTEL
		</button>
		{#if showFact}
			<p class="mt-4 text-sm" style="color: var(--av-green);">
				<span style="color: var(--av-amber-dim);">[INTEL]</span> {facts[factIndex]}
			</p>
		{/if}
	</div>

	<!-- Interests as flight badges -->
	<div class="instrument-panel p-6">
		<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">▸ specializations</p>
		<div class="flex flex-wrap gap-2">
			{#each interests as interest}
				<span class="text-xs px-3 py-1.5 rounded-full transition-all duration-200"
					style="border: 1px solid var(--av-panel-border); color: var(--av-amber-dim);"
					onmouseenter={(e) => { e.currentTarget.style.borderColor = 'var(--av-amber)'; e.currentTarget.style.color = 'var(--av-amber)'; }}
					onmouseleave={(e) => { e.currentTarget.style.borderColor = 'var(--av-panel-border)'; e.currentTarget.style.color = 'var(--av-amber-dim)'; }}>
					{interest.icon} {interest.name}
				</span>
			{/each}
		</div>
	</div>
</div>
