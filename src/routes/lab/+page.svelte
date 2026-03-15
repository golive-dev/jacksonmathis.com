<script lang="ts">
	import { onMount } from 'svelte';

	// === Experiment 1: Color mixer ===
	let red = $state(57);
	let green = $state(255);
	let blue = $state(20);
	const hexColor = $derived(
		'#' + [red, green, blue].map(c => c.toString(16).padStart(2, '0')).join('')
	);

	// === Experiment 2: Gravity balls ===
	let canvasEl: HTMLCanvasElement;
	let balls: { x: number; y: number; vx: number; vy: number; r: number; color: string }[] = [];
	const colors = ['#39ff14', '#00f0ff', '#ff2d95', '#b026ff', '#fff01f'];

	function spawnBall(x: number, y: number) {
		balls.push({
			x, y,
			vx: (Math.random() - 0.5) * 4,
			vy: (Math.random() - 0.5) * 4,
			r: 6 + Math.random() * 10,
			color: colors[Math.floor(Math.random() * colors.length)],
		});
		if (balls.length > 50) balls.shift();
	}

	onMount(() => {
		const ctx = canvasEl.getContext('2d')!;
		const resize = () => {
			canvasEl.width = canvasEl.clientWidth;
			canvasEl.height = canvasEl.clientHeight;
		};
		resize();
		window.addEventListener('resize', resize);

		let raf: number;
		function loop() {
			ctx.fillStyle = 'rgba(10, 10, 15, 0.15)';
			ctx.fillRect(0, 0, canvasEl.width, canvasEl.height);
			for (const b of balls) {
				b.vy += 0.15; // gravity
				b.x += b.vx;
				b.y += b.vy;
				// bounce
				if (b.x - b.r < 0) { b.x = b.r; b.vx *= -0.8; }
				if (b.x + b.r > canvasEl.width) { b.x = canvasEl.width - b.r; b.vx *= -0.8; }
				if (b.y + b.r > canvasEl.height) { b.y = canvasEl.height - b.r; b.vy *= -0.8; }
				if (b.y - b.r < 0) { b.y = b.r; b.vy *= -0.8; }
				// draw
				ctx.beginPath();
				ctx.arc(b.x, b.y, b.r, 0, Math.PI * 2);
				ctx.fillStyle = b.color;
				ctx.fill();
				ctx.shadowBlur = 15;
				ctx.shadowColor = b.color;
			}
			ctx.shadowBlur = 0;
			raf = requestAnimationFrame(loop);
		}
		loop();

		return () => {
			cancelAnimationFrame(raf);
			window.removeEventListener('resize', resize);
		};
	});

	// === Experiment 3: ASCII art ===
	let asciiInput = $state('JACKSON');
	const asciiMap: Record<string, string[]> = {
		A: ['  █  ','█   █','█████','█   █','█   █'],
		B: ['████ ','█   █','████ ','█   █','████ '],
		C: [' ████','█    ','█    ','█    ',' ████'],
		D: ['████ ','█   █','█   █','█   █','████ '],
		E: ['█████','█    ','███  ','█    ','█████'],
		F: ['█████','█    ','███  ','█    ','█    '],
		G: [' ████','█    ','█  ██','█   █',' ████'],
		H: ['█   █','█   █','█████','█   █','█   █'],
		I: ['█████','  █  ','  █  ','  █  ','█████'],
		J: ['█████','   █ ','   █ ','█  █ ',' ██  '],
		K: ['█  █ ','█ █  ','██   ','█ █  ','█  █ '],
		L: ['█    ','█    ','█    ','█    ','█████'],
		M: ['█   █','██ ██','█ █ █','█   █','█   █'],
		N: ['█   █','██  █','█ █ █','█  ██','█   █'],
		O: [' ███ ','█   █','█   █','█   █',' ███ '],
		P: ['████ ','█   █','████ ','█    ','█    '],
		Q: [' ███ ','█   █','█ █ █','█  █ ',' ██ █'],
		R: ['████ ','█   █','████ ','█ █  ','█  █ '],
		S: [' ████','█    ',' ███ ','    █','████ '],
		T: ['█████','  █  ','  █  ','  █  ','  █  '],
		U: ['█   █','█   █','█   █','█   █',' ███ '],
		V: ['█   █','█   █','█   █',' █ █ ','  █  '],
		W: ['█   █','█   █','█ █ █','██ ██','█   █'],
		X: ['█   █',' █ █ ','  █  ',' █ █ ','█   █'],
		Y: ['█   █',' █ █ ','  █  ','  █  ','  █  '],
		Z: ['█████','   █ ','  █  ',' █   ','█████'],
		' ': ['     ','     ','     ','     ','     '],
	};

	const asciiOutput = $derived(
		Array.from({ length: 5 }, (_, row) =>
			asciiInput.toUpperCase().split('').map(ch => asciiMap[ch]?.[row] ?? '     ').join(' ')
		).join('\n')
	);
</script>

<div class="min-h-screen px-6 py-20 max-w-3xl mx-auto">
	<a href="/" class="font-mono text-xs text-gray-600 hover:text-neon-green transition-colors">← back</a>

	<h1 class="font-[family-name:var(--font-family-display)] text-4xl md:text-5xl font-bold mt-8 mb-2">
		<span class="text-neon-pink glow-pink">/lab</span>
	</h1>
	<p class="text-gray-500 font-mono text-sm mb-12">interactive experiments — click around, break stuff</p>

	<!-- Experiment 1: Color Mixer -->
	<section class="bg-dark-card border border-dark-border rounded-lg p-6 mb-8">
		<h2 class="font-mono text-neon-green text-sm mb-4">01 // color mixer</h2>
		<div class="flex items-center gap-6">
			<div class="w-20 h-20 rounded-lg border border-dark-border transition-colors duration-200" style="background-color: {hexColor}; box-shadow: 0 0 30px {hexColor}40;"></div>
			<div class="flex-1 space-y-3">
				<label class="flex items-center gap-3 font-mono text-xs">
					<span class="text-red-400 w-6">R</span>
					<input type="range" min="0" max="255" bind:value={red} class="flex-1 accent-red-500" />
					<span class="text-gray-500 w-8">{red}</span>
				</label>
				<label class="flex items-center gap-3 font-mono text-xs">
					<span class="text-green-400 w-6">G</span>
					<input type="range" min="0" max="255" bind:value={green} class="flex-1 accent-green-500" />
					<span class="text-gray-500 w-8">{green}</span>
				</label>
				<label class="flex items-center gap-3 font-mono text-xs">
					<span class="text-blue-400 w-6">B</span>
					<input type="range" min="0" max="255" bind:value={blue} class="flex-1 accent-blue-500" />
					<span class="text-gray-500 w-8">{blue}</span>
				</label>
			</div>
		</div>
		<p class="font-mono text-xs text-gray-500 mt-3">hex: <span class="text-white">{hexColor}</span></p>
	</section>

	<!-- Experiment 2: Gravity Balls -->
	<section class="bg-dark-card border border-dark-border rounded-lg p-6 mb-8">
		<h2 class="font-mono text-neon-blue text-sm mb-4">02 // gravity sandbox</h2>
		<p class="font-mono text-xs text-gray-500 mb-3">click to spawn balls. they have physics.</p>
		<canvas
			bind:this={canvasEl}
			class="w-full h-64 rounded-lg border border-dark-border cursor-crosshair bg-dark"
			onclick={(e) => {
				const rect = canvasEl.getBoundingClientRect();
				spawnBall(e.clientX - rect.left, e.clientY - rect.top);
			}}
		></canvas>
		<p class="font-mono text-xs text-gray-600 mt-2">balls: {balls.length}</p>
	</section>

	<!-- Experiment 3: ASCII Art -->
	<section class="bg-dark-card border border-dark-border rounded-lg p-6 mb-8">
		<h2 class="font-mono text-neon-pink text-sm mb-4">03 // ascii art generator</h2>
		<input
			type="text"
			bind:value={asciiInput}
			maxlength="10"
			placeholder="type something..."
			class="w-full bg-dark border border-dark-border rounded-lg px-4 py-2 font-mono text-sm text-white placeholder-gray-600
				   focus:outline-none focus:border-neon-pink/50 transition-colors"
		/>
		<pre class="mt-4 font-mono text-[8px] md:text-xs text-neon-green leading-tight overflow-x-auto">{asciiOutput}</pre>
	</section>
</div>
