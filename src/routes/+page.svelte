<script lang="ts">
	import { onMount } from 'svelte';

	let mounted = $state(false);
	let clickCount = $state(0);
	let konamiProgress = $state(0);
	let showSecret = $state(false);
	let mouseX = $state(0);
	let mouseY = $state(0);
	let showTrail = $state(false);

	const konamiCode = ['ArrowUp','ArrowUp','ArrowDown','ArrowDown','ArrowLeft','ArrowRight','ArrowLeft','ArrowRight','b','a'];

	const skills = ['gaming', 'music', 'football', 'the internet', 'building things'];
	let currentSkill = $state(0);

	onMount(() => {
		mounted = true;
		const interval = setInterval(() => {
			currentSkill = (currentSkill + 1) % skills.length;
		}, 2000);

		const handleKey = (e: KeyboardEvent) => {
			if (e.key === konamiCode[konamiProgress]) {
				konamiProgress++;
				if (konamiProgress === konamiCode.length) {
					showSecret = true;
					konamiProgress = 0;
				}
			} else {
				konamiProgress = 0;
			}
		};

		window.addEventListener('keydown', handleKey);
		return () => {
			clearInterval(interval);
			window.removeEventListener('keydown', handleKey);
		};
	});

	function handleClick() {
		clickCount++;
	}

	function handleMouseMove(e: MouseEvent) {
		mouseX = e.clientX;
		mouseY = e.clientY;
	}

	const clickMessages: Record<number, string> = {
		1: 'nice.',
		5: 'okay you like clicking.',
		10: 'seriously?',
		25: 'you have commitment issues... in a good way.',
		50: 'you found dedication.',
		100: '🏆 you are the click champion.',
		200: 'go outside.',
	};

	const clickMessage = $derived(
		Object.entries(clickMessages)
			.reverse()
			.find(([threshold]) => clickCount >= Number(threshold))?.[1] ?? ''
	);
</script>

<div class="relative min-h-screen overflow-hidden" onmousemove={handleMouseMove}>
	<!-- Ambient background grid -->
	<div class="pointer-events-none fixed inset-0 opacity-[0.03]"
		style="background-image: linear-gradient(rgba(57,255,20,.5) 1px, transparent 1px), linear-gradient(90deg, rgba(57,255,20,.5) 1px, transparent 1px); background-size: 50px 50px;">
	</div>

	<!-- Cursor glow -->
	<div class="pointer-events-none fixed w-64 h-64 rounded-full opacity-10 transition-all duration-300 ease-out -translate-x-1/2 -translate-y-1/2"
		style="left: {mouseX}px; top: {mouseY}px; background: radial-gradient(circle, var(--color-neon-green), transparent 70%);">
	</div>

	<main class="relative z-10 flex flex-col items-center justify-center min-h-screen px-6 py-20">
		<!-- Hero -->
		<div class="text-center space-y-6 {mounted ? 'opacity-100 translate-y-0' : 'opacity-0 translate-y-8'} transition-all duration-1000 ease-out">
			
			<div class="inline-block mb-4">
				<span class="text-xs font-mono tracking-[0.3em] uppercase text-neon-green/60 border border-neon-green/20 px-4 py-1.5 rounded-full">
					v1.0.0 — online
				</span>
			</div>

			<h1 class="font-[family-name:var(--font-family-display)] text-5xl md:text-7xl lg:text-8xl font-bold tracking-tight">
				<span class="text-white">Jackson</span>
				<span class="text-neon-green glow-green"> Mathis</span>
			</h1>

			<p class="text-lg md:text-xl text-gray-400 max-w-lg mx-auto font-mono">
				into <span class="text-neon-blue glow-blue transition-all duration-500">{skills[currentSkill]}</span>
			</p>

			<!-- Terminal-style block -->
			<div class="mt-10 bg-dark-card border border-dark-border rounded-lg p-5 max-w-md mx-auto text-left font-mono text-sm">
				<div class="flex items-center gap-2 mb-3">
					<div class="w-3 h-3 rounded-full bg-red-500/80"></div>
					<div class="w-3 h-3 rounded-full bg-yellow-500/80"></div>
					<div class="w-3 h-3 rounded-full bg-green-500/80"></div>
					<span class="text-gray-600 text-xs ml-2">jackson@earth ~ %</span>
				</div>
				<p class="text-gray-500">$ whoami</p>
				<p class="text-neon-green">jackson.mathis</p>
				<p class="text-gray-500 mt-2">$ cat interests.txt</p>
				<p class="text-gray-300">code, music, games, the internet</p>
				<p class="text-gray-500 mt-2">$ python -c "import antigravity"</p>
				<p class="text-neon-pink">🚀 whoooosh</p>
				<p class="text-gray-500 mt-2">$ <span class="border-r-2 border-neon-green animate-[blink-caret_1s_step-end_infinite]">&nbsp;</span></p>
			</div>
		</div>

		<!-- Click counter -->
		<div class="mt-16 text-center {mounted ? 'opacity-100' : 'opacity-0'} transition-all duration-1000 delay-500">
			<button
				onclick={handleClick}
				class="group relative px-8 py-3 font-mono text-sm border border-neon-green/30 rounded-lg
					   text-neon-green hover:bg-neon-green/10 hover:border-neon-green/60
					   transition-all duration-200 active:scale-95 cursor-pointer"
			>
				<span class="relative z-10">clicks: {clickCount}</span>
				<div class="absolute inset-0 rounded-lg opacity-0 group-hover:opacity-100 transition-opacity duration-300"
					style="box-shadow: 0 0 20px rgba(57,255,20,0.15), inset 0 0 20px rgba(57,255,20,0.05);">
				</div>
			</button>
			{#if clickMessage}
				<p class="mt-3 text-xs font-mono text-gray-500 animate-[pulse-glow_2s_ease-in-out_infinite]">{clickMessage}</p>
			{/if}
		</div>

		<!-- Nav links -->
		<nav class="mt-16 flex flex-wrap justify-center gap-4 {mounted ? 'opacity-100' : 'opacity-0'} transition-all duration-1000 delay-700">
			<a href="/games" class="font-mono text-sm text-gray-400 hover:text-neon-yellow border-b border-transparent hover:border-neon-yellow/50 pb-0.5 transition-all duration-200">
				/games
			</a>
			<a href="/projects" class="font-mono text-sm text-gray-400 hover:text-neon-green border-b border-transparent hover:border-neon-green/50 pb-0.5 transition-all duration-200">
				/projects
			</a>
			<a href="/about" class="font-mono text-sm text-gray-400 hover:text-neon-blue border-b border-transparent hover:border-neon-blue/50 pb-0.5 transition-all duration-200">
				/about
			</a>
			<a href="/lab" class="font-mono text-sm text-gray-400 hover:text-neon-pink border-b border-transparent hover:border-neon-pink/50 pb-0.5 transition-all duration-200">
				/lab
			</a>
		</nav>

		<!-- Konami secret -->
		{#if showSecret}
			<div class="fixed inset-0 z-50 flex items-center justify-center bg-black/90 backdrop-blur-sm"
				role="dialog">
				<div class="text-center space-y-4 animate-[float_3s_ease-in-out_infinite]">
					<p class="text-6xl">🎮</p>
					<p class="font-mono text-neon-green glow-green text-2xl">KONAMI CODE ACTIVATED</p>
					<p class="font-mono text-gray-400 text-sm">you found the secret. respect.</p>
					<button onclick={() => showSecret = false}
						class="mt-4 font-mono text-xs text-gray-600 hover:text-neon-green transition-colors cursor-pointer">
						[close]
					</button>
				</div>
			</div>
		{/if}

		<!-- Footer -->
		<footer class="mt-auto pt-20 pb-8 text-center {mounted ? 'opacity-100' : 'opacity-0'} transition-all duration-1000 delay-1000">
			<p class="font-mono text-xs text-gray-700">
				© {new Date().getFullYear()} jackson mathis
			</p>
		</footer>
	</main>
</div>
