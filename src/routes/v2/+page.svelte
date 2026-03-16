<script lang="ts">
	import { onMount } from 'svelte';

	let mounted = $state(false);
	let altitude = $state(0);
	let heading = $state(0);
	let airspeed = $state(0);
	let radarAngle = $state(0);
	let localTime = $state('--:--:--');

	const callsigns = ['MAVERICK', 'GHOST RIDER', 'VIPER', 'PHOENIX', 'ROOSTER'];
	let callsignIdx = $state(0);

	const interests = ['football', 'gaming', 'music', 'airplanes', 'building things', 'the internet'];
	let interestIdx = $state(0);

	onMount(() => {
		mounted = true;

		// Simulate climbing instruments
		const climbInterval = setInterval(() => {
			if (altitude < 35000) altitude += Math.floor(Math.random() * 800 + 200);
			else clearInterval(climbInterval);
		}, 100);

		const headingInterval = setInterval(() => {
			heading = (heading + 1) % 360;
		}, 50);

		const speedInterval = setInterval(() => {
			if (airspeed < 485) airspeed += Math.floor(Math.random() * 20 + 5);
			else clearInterval(speedInterval);
		}, 80);

		const radarInterval = setInterval(() => {
			radarAngle = (radarAngle + 3) % 360;
		}, 30);

		const clockInterval = setInterval(() => {
			const now = new Date();
			localTime = now.toTimeString().split(' ')[0];
		}, 1000);

		const callsignInterval = setInterval(() => {
			callsignIdx = (callsignIdx + 1) % callsigns.length;
		}, 3000);

		const interestInterval = setInterval(() => {
			interestIdx = (interestIdx + 1) % interests.length;
		}, 2000);

		return () => {
			clearInterval(climbInterval);
			clearInterval(headingInterval);
			clearInterval(speedInterval);
			clearInterval(radarInterval);
			clearInterval(clockInterval);
			clearInterval(callsignInterval);
			clearInterval(interestInterval);
		};
	});

	const headingLetter = $derived(
		heading < 23 ? 'N' : heading < 68 ? 'NE' : heading < 113 ? 'E' :
		heading < 158 ? 'SE' : heading < 203 ? 'S' : heading < 248 ? 'SW' :
		heading < 293 ? 'W' : heading < 338 ? 'NW' : 'N'
	);
</script>

<div class="relative min-h-screen overflow-hidden">
	<!-- Stars background -->
	<div class="pointer-events-none fixed inset-0">
		{#each Array(40) as _, i}
			<div class="absolute rounded-full bg-white"
				style="width: {1 + Math.random() * 2}px; height: {1 + Math.random() * 2}px;
					   left: {Math.random() * 100}%; top: {Math.random() * 60}%;
					   opacity: {0.2 + Math.random() * 0.5};
					   animation: blink-slow {2 + Math.random() * 4}s ease-in-out infinite {Math.random() * 3}s;">
			</div>
		{/each}
	</div>

	<!-- Horizon line -->
	<div class="pointer-events-none fixed left-0 right-0 top-[55%] h-px opacity-20"
		style="background: linear-gradient(90deg, transparent, var(--av-amber), transparent);
			   animation: horizon-drift 8s ease-in-out infinite;">
	</div>

	<main class="relative z-10 px-6 py-16 max-w-4xl mx-auto">

		<!-- Top bar: flight info strip -->
		<div class="flex items-center justify-between text-xs mb-12 opacity-60"
			style="animation: altitude-rise 1s ease-out;">
			<span style="color: var(--av-amber);">FLT JM-001</span>
			<span style="color: var(--av-green);">ZULU {localTime}</span>
			<span style="color: var(--av-amber);">CALLSIGN: {callsigns[callsignIdx]}</span>
		</div>

		<!-- HERO: Name as flight designator -->
		<div class="text-center mb-16 {mounted ? 'opacity-100' : 'opacity-0'} transition-opacity duration-1000"
			style="animation: altitude-rise 0.8s ease-out;">

			<p class="text-xs tracking-[0.5em] uppercase mb-6" style="color: var(--av-amber-dim);">
				▸ pilot profile loaded ▸
			</p>

			<h1 class="text-5xl md:text-7xl lg:text-8xl font-bold tracking-tight mb-4"
				style="font-family: 'Orbitron', sans-serif;">
				<span class="text-white">JACKSON</span><br />
				<span class="glow-amber" style="color: var(--av-amber);">MATHIS</span>
			</h1>

			<p class="text-lg mt-6" style="color: var(--av-white); opacity: 0.6;">
				into
				<span class="glow-amber transition-all duration-500" style="color: var(--av-amber);">
					{interests[interestIdx]}
				</span>
			</p>
		</div>

		<!-- Instrument Panel Row -->
		<div class="grid grid-cols-3 gap-4 mb-12" style="animation: altitude-rise 1.2s ease-out;">

			<!-- Altimeter -->
			<div class="instrument-panel p-5 text-center">
				<p class="text-[10px] tracking-widest uppercase mb-2" style="color: var(--av-amber-dim);">altitude</p>
				<p class="text-3xl font-bold tabular-nums" style="color: var(--av-green); font-family: 'Orbitron', sans-serif;">
					{altitude.toLocaleString()}
				</p>
				<p class="text-[10px] mt-1" style="color: var(--av-green-dim);">FT MSL</p>
			</div>

			<!-- Heading -->
			<div class="instrument-panel p-5 text-center">
				<p class="text-[10px] tracking-widest uppercase mb-2" style="color: var(--av-amber-dim);">heading</p>
				<p class="text-3xl font-bold tabular-nums" style="color: var(--av-amber); font-family: 'Orbitron', sans-serif;">
					{String(heading).padStart(3, '0')}°
				</p>
				<p class="text-[10px] mt-1" style="color: var(--av-amber-dim);">{headingLetter}</p>
			</div>

			<!-- Airspeed -->
			<div class="instrument-panel p-5 text-center">
				<p class="text-[10px] tracking-widest uppercase mb-2" style="color: var(--av-amber-dim);">airspeed</p>
				<p class="text-3xl font-bold tabular-nums" style="color: var(--av-green); font-family: 'Orbitron', sans-serif;">
					{airspeed}
				</p>
				<p class="text-[10px] mt-1" style="color: var(--av-green-dim);">KIAS</p>
			</div>
		</div>

		<!-- Radar + Flight log -->
		<div class="grid md:grid-cols-2 gap-4 mb-12" style="animation: altitude-rise 1.4s ease-out;">

			<!-- Mini Radar -->
			<div class="instrument-panel p-6 flex flex-col items-center justify-center">
				<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">radar</p>
				<div class="relative w-32 h-32">
					<!-- Radar circles -->
					<div class="absolute inset-0 rounded-full border opacity-20" style="border-color: var(--av-green);"></div>
					<div class="absolute inset-4 rounded-full border opacity-15" style="border-color: var(--av-green);"></div>
					<div class="absolute inset-8 rounded-full border opacity-10" style="border-color: var(--av-green);"></div>
					<!-- Cross hairs -->
					<div class="absolute top-0 bottom-0 left-1/2 w-px opacity-10" style="background: var(--av-green);"></div>
					<div class="absolute left-0 right-0 top-1/2 h-px opacity-10" style="background: var(--av-green);"></div>
					<!-- Sweep -->
					<div class="absolute inset-0 rounded-full"
						style="background: conic-gradient(from {radarAngle}deg, transparent 0deg, rgba(0, 255, 136, 0.15) 30deg, transparent 60deg);"></div>
					<!-- Center dot -->
					<div class="absolute top-1/2 left-1/2 w-2 h-2 rounded-full -translate-x-1/2 -translate-y-1/2"
						style="background: var(--av-green); box-shadow: 0 0 8px var(--av-green);"></div>
					<!-- Blips -->
					<div class="absolute w-1.5 h-1.5 rounded-full" style="top: 25%; left: 60%; background: var(--av-amber); box-shadow: 0 0 6px var(--av-amber); animation: blink-slow 2s infinite;"></div>
					<div class="absolute w-1 h-1 rounded-full" style="top: 65%; left: 30%; background: var(--av-amber); box-shadow: 0 0 4px var(--av-amber); animation: blink-slow 3s infinite 1s;"></div>
				</div>
			</div>

			<!-- Flight Log / Terminal -->
			<div class="instrument-panel p-6">
				<p class="text-[10px] tracking-widest uppercase mb-4" style="color: var(--av-amber-dim);">flight log</p>
				<div class="space-y-2 text-xs" style="color: var(--av-green);">
					<p><span style="color: var(--av-amber-dim);">[SYS]</span> pilot profile loaded</p>
					<p><span style="color: var(--av-amber-dim);">[NAV]</span> heading locked — 270°W</p>
					<p><span style="color: var(--av-amber-dim);">[ALT]</span> climbing to FL350</p>
					<p><span style="color: var(--av-amber-dim);">[COM]</span> "this is jackson, ready to fly"</p>
					<p style="color: var(--av-amber); animation: blink-slow 1.5s infinite;">▸ CLEAR FOR TAKEOFF _</p>
				</div>
			</div>
		</div>

		<!-- Navigation strip -->
		<div class="instrument-panel p-4" style="animation: altitude-rise 1.6s ease-out;">
			<p class="text-[10px] tracking-widest uppercase mb-4 text-center" style="color: var(--av-amber-dim);">navigation</p>
			<div class="flex justify-center gap-3 flex-wrap">
				{#each [
					{ href: '/v2/about', label: 'ABOUT', icon: '📋' },
					{ href: '/v2/projects', label: 'PROJECTS', icon: '🛠' },
					{ href: '/v2/hangar', label: 'HANGAR', icon: '🏗' },
					{ href: '/v2/games', label: 'GAMES', icon: '🎮' },
					{ href: '/', label: 'V1 — HACKER', icon: '💻' },
				] as nav}
					<a href={nav.href}
						class="text-xs px-4 py-2 rounded transition-all duration-200 hover:scale-105"
						style="border: 1px solid var(--av-panel-border); color: var(--av-amber);
							   background: var(--av-instrument);"
						onmouseenter={(e) => { e.currentTarget.style.borderColor = 'var(--av-amber)'; e.currentTarget.style.boxShadow = '0 0 12px rgba(255,176,0,0.2)'; }}
						onmouseleave={(e) => { e.currentTarget.style.borderColor = 'var(--av-panel-border)'; e.currentTarget.style.boxShadow = 'none'; }}>
						{nav.icon} {nav.label}
					</a>
				{/each}
			</div>
		</div>

		<!-- Footer -->
		<div class="text-center mt-16 text-xs opacity-30" style="color: var(--av-amber-dim);">
			© 2026 jackson mathis — pilot edition
		</div>

	</main>
</div>
