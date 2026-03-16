<script lang="ts">
	import { onMount } from 'svelte';

	// === Altimeter Widget ===
	let altInput = $state(35000);
	const altDescription = $derived(
		altInput < 1000 ? 'Ground level — taxiing' :
		altInput < 10000 ? 'Low altitude — departure/approach' :
		altInput < 25000 ? 'Mid altitude — regional flights' :
		altInput < 40000 ? 'Cruise altitude — commercial jets' :
		altInput < 60000 ? 'High altitude — military / U-2 territory' :
		'Edge of space — SR-71 Blackbird domain'
	);

	// === Aircraft Spotter ===
	const aircraft = [
		{ name: 'Cessna 172', type: 'Single Engine', speed: '140 kts', ceiling: '14,000 ft', icon: '🛩️', fact: 'Most produced aircraft ever — over 44,000 built.' },
		{ name: 'Boeing 737', type: 'Narrow-body Jet', speed: '453 kts', ceiling: '41,000 ft', icon: '✈️', fact: 'Most popular commercial airliner in history.' },
		{ name: 'F-22 Raptor', type: 'Stealth Fighter', speed: '1,498 kts', ceiling: '65,000 ft', icon: '🔥', fact: 'Can supercruise at Mach 1.5 without afterburners.' },
		{ name: 'SR-71 Blackbird', type: 'Reconnaissance', speed: '2,193 kts', ceiling: '85,000 ft', icon: '⚡', fact: 'Still the fastest air-breathing manned aircraft ever.' },
		{ name: 'Boeing 747', type: 'Wide-body Jet', speed: '490 kts', ceiling: '45,000 ft', icon: '👑', fact: 'Nicknamed "Queen of the Skies" — first wide-body ever.' },
		{ name: 'P-51 Mustang', type: 'WWII Fighter', speed: '383 kts', ceiling: '41,900 ft', icon: '⭐', fact: 'Changed the course of WWII by escorting bombers to Berlin.' },
	];
	let selectedPlane = $state(0);

	// === NATO Alphabet ===
	let natoInput = $state('JACKSON');
	const natoAlphabet: Record<string, string> = {
		A: 'Alpha', B: 'Bravo', C: 'Charlie', D: 'Delta', E: 'Echo', F: 'Foxtrot',
		G: 'Golf', H: 'Hotel', I: 'India', J: 'Juliet', K: 'Kilo', L: 'Lima',
		M: 'Mike', N: 'November', O: 'Oscar', P: 'Papa', Q: 'Quebec', R: 'Romeo',
		S: 'Sierra', T: 'Tango', U: 'Uniform', V: 'Victor', W: 'Whiskey',
		X: 'X-ray', Y: 'Yankee', Z: 'Zulu',
	};
	const natoOutput = $derived(
		natoInput.toUpperCase().split('').map(ch => natoAlphabet[ch] || ch).join(' · ')
	);
</script>

<div class="min-h-screen px-6 py-16 max-w-3xl mx-auto">
	<a href="/v2" class="text-xs transition-colors duration-200" style="color: var(--av-amber-dim);"
		onmouseenter={(e) => e.currentTarget.style.color = 'var(--av-amber)'}
		onmouseleave={(e) => e.currentTarget.style.color = 'var(--av-amber-dim)'}>
		← RETURN TO COCKPIT
	</a>

	<h1 class="text-4xl md:text-5xl font-bold mt-8 mb-2" style="font-family: 'Orbitron', sans-serif;">
		<span class="glow-amber" style="color: var(--av-amber);">THE HANGAR</span>
	</h1>
	<p class="text-xs mb-12" style="color: var(--av-amber-dim);">INTERACTIVE AVIATION LAB — EXPERIMENT FREELY</p>

	<!-- Experiment 1: Altimeter -->
	<section class="instrument-panel p-6 mb-6">
		<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">01 // altimeter simulator</p>
		<div class="flex items-center gap-6">
			<div class="text-center flex-shrink-0" style="width: 100px;">
				<p class="text-3xl font-bold tabular-nums" style="color: var(--av-green); font-family: 'Orbitron', sans-serif;">
					{(altInput / 1000).toFixed(0)}
				</p>
				<p class="text-[10px]" style="color: var(--av-green-dim);">× 1000 FT</p>
			</div>
			<div class="flex-1">
				<input type="range" min="0" max="85000" step="500" bind:value={altInput}
					class="w-full" style="accent-color: var(--av-amber);" />
				<p class="text-xs mt-2" style="color: var(--av-amber);">{altInput.toLocaleString()} ft — {altDescription}</p>
			</div>
		</div>
	</section>

	<!-- Experiment 2: Aircraft Spotter -->
	<section class="instrument-panel p-6 mb-6">
		<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">02 // aircraft spotter</p>
		<div class="flex gap-2 flex-wrap mb-4">
			{#each aircraft as plane, i}
				<button
					onclick={() => selectedPlane = i}
					class="text-xs px-3 py-1.5 rounded transition-all duration-200 cursor-pointer"
					style="border: 1px solid {selectedPlane === i ? 'var(--av-amber)' : 'var(--av-panel-border)'};
						   color: {selectedPlane === i ? 'var(--av-amber)' : 'var(--av-amber-dim)'};
						   background: {selectedPlane === i ? 'rgba(255,176,0,0.1)' : 'transparent'};">
					{plane.icon} {plane.name}
				</button>
			{/each}
		</div>
		{@const plane = aircraft[selectedPlane]}
		<div class="grid grid-cols-3 gap-4 mb-3">
			<div>
				<p class="text-[10px] tracking-widest" style="color: var(--av-amber-dim);">TYPE</p>
				<p class="text-xs mt-0.5" style="color: var(--av-white);">{plane.type}</p>
			</div>
			<div>
				<p class="text-[10px] tracking-widest" style="color: var(--av-amber-dim);">MAX SPEED</p>
				<p class="text-xs mt-0.5" style="color: var(--av-green);">{plane.speed}</p>
			</div>
			<div>
				<p class="text-[10px] tracking-widest" style="color: var(--av-amber-dim);">CEILING</p>
				<p class="text-xs mt-0.5" style="color: var(--av-green);">{plane.ceiling}</p>
			</div>
		</div>
		<p class="text-xs" style="color: var(--av-amber);">
			<span style="color: var(--av-amber-dim);">[FUN FACT]</span> {plane.fact}
		</p>
	</section>

	<!-- Experiment 3: NATO Phonetic Alphabet -->
	<section class="instrument-panel p-6">
		<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">03 // nato phonetic encoder</p>
		<input
			type="text"
			bind:value={natoInput}
			maxlength="12"
			placeholder="TYPE YOUR CALLSIGN..."
			class="w-full rounded px-4 py-2 text-sm uppercase placeholder-gray-700 focus:outline-none transition-colors"
			style="background: var(--av-panel); border: 1px solid var(--av-panel-border); color: var(--av-white);"
			onfocus={(e) => e.currentTarget.style.borderColor = 'var(--av-amber)'}
			onblur={(e) => e.currentTarget.style.borderColor = 'var(--av-panel-border)'}
		/>
		<p class="mt-3 text-sm" style="color: var(--av-green);">{natoOutput}</p>
	</section>
</div>
