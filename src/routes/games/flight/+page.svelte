<script lang="ts">
	import { onMount } from 'svelte';

	let canvas: HTMLCanvasElement;
	let score = $state(0);
	let gameOver = $state(false);
	let gameStarted = $state(false);
	let highScore = $state(0);

	onMount(() => {
		const ctx = canvas.getContext('2d')!;
		const W = 600;
		const H = 400;
		canvas.width = W;
		canvas.height = H;

		let planeY = H / 2;
		let planeVel = 0;
		let dist = 0;
		let mountains: { x: number; gapY: number; gapH: number; passed: boolean }[] = [];
		let stars: { x: number; y: number; s: number; b: number }[] = [];
		let particles: { x: number; y: number; vx: number; vy: number; life: number; color: string }[] = [];
		let frame = 0;
		let speed = 2.5;
		let running = false;

		// Generate starfield
		for (let i = 0; i < 80; i++) {
			stars.push({ x: Math.random() * W, y: Math.random() * H, s: Math.random() * 2 + 0.5, b: Math.random() });
		}

		function spawnMountain() {
			const gapH = 130 - Math.min(dist / 500, 40);
			const gapY = Math.random() * (H - gapH - 80) + 40;
			mountains.push({ x: W + 60, gapY, gapH, passed: false });
		}

		function reset() {
			planeY = H / 2;
			planeVel = 0;
			dist = 0;
			speed = 2.5;
			mountains = [];
			particles = [];
			frame = 0;
			score = 0;
			gameOver = false;
			running = true;
			gameStarted = true;
			spawnMountain();
		}

		function addTrail() {
			particles.push({
				x: 60,
				y: planeY + 6,
				vx: -Math.random() * 2 - 1,
				vy: (Math.random() - 0.5) * 1.5,
				life: 1,
				color: Math.random() > 0.5 ? '#39ff14' : '#00f0ff',
			});
		}

		function drawPlane(px: number, py: number) {
			// Body
			ctx.fillStyle = '#ffffff';
			ctx.beginPath();
			ctx.moveTo(px + 20, py);
			ctx.lineTo(px - 12, py - 5);
			ctx.lineTo(px - 12, py + 5);
			ctx.closePath();
			ctx.fill();

			// Wings
			ctx.fillStyle = '#00f0ff';
			ctx.beginPath();
			ctx.moveTo(px, py - 2);
			ctx.lineTo(px - 8, py - 14);
			ctx.lineTo(px - 10, py - 2);
			ctx.closePath();
			ctx.fill();
			ctx.beginPath();
			ctx.moveTo(px, py + 2);
			ctx.lineTo(px - 8, py + 14);
			ctx.lineTo(px - 10, py + 2);
			ctx.closePath();
			ctx.fill();

			// Tail
			ctx.fillStyle = '#ff2d95';
			ctx.beginPath();
			ctx.moveTo(px - 12, py - 5);
			ctx.lineTo(px - 18, py - 12);
			ctx.lineTo(px - 14, py - 5);
			ctx.closePath();
			ctx.fill();

			// Engine glow
			ctx.shadowBlur = 12;
			ctx.shadowColor = '#39ff14';
			ctx.fillStyle = '#39ff14';
			ctx.beginPath();
			ctx.arc(px - 14, py, 2 + Math.sin(frame * 0.3) * 1, 0, Math.PI * 2);
			ctx.fill();
			ctx.shadowBlur = 0;
		}

		function drawMountain(m: { x: number; gapY: number; gapH: number }) {
			// Top mountain
			ctx.fillStyle = '#1a1a2e';
			ctx.strokeStyle = '#b026ff';
			ctx.lineWidth = 2;
			ctx.beginPath();
			ctx.moveTo(m.x - 30, 0);
			ctx.lineTo(m.x - 30, m.gapY);
			ctx.lineTo(m.x - 15, m.gapY - 10);
			ctx.lineTo(m.x, m.gapY);
			ctx.lineTo(m.x + 15, m.gapY - 5);
			ctx.lineTo(m.x + 30, m.gapY);
			ctx.lineTo(m.x + 30, 0);
			ctx.closePath();
			ctx.fill();
			ctx.stroke();

			// Bottom mountain
			const botY = m.gapY + m.gapH;
			ctx.beginPath();
			ctx.moveTo(m.x - 30, H);
			ctx.lineTo(m.x - 30, botY);
			ctx.lineTo(m.x - 15, botY + 10);
			ctx.lineTo(m.x, botY);
			ctx.lineTo(m.x + 15, botY + 5);
			ctx.lineTo(m.x + 30, botY);
			ctx.lineTo(m.x + 30, H);
			ctx.closePath();
			ctx.fill();
			ctx.stroke();
		}

		function loop() {
			ctx.fillStyle = '#0a0a0f';
			ctx.fillRect(0, 0, W, H);

			// Stars
			for (const s of stars) {
				const flicker = 0.5 + Math.sin(frame * 0.02 + s.b * 10) * 0.5;
				ctx.fillStyle = `rgba(255, 255, 255, ${flicker * 0.6})`;
				ctx.fillRect(s.x, s.y, s.s, s.s);
				if (running) {
					s.x -= speed * 0.3;
					if (s.x < 0) { s.x = W; s.y = Math.random() * H; }
				}
			}

			// Ground line
			ctx.strokeStyle = '#39ff1440';
			ctx.lineWidth = 1;
			ctx.beginPath();
			ctx.moveTo(0, H - 1);
			ctx.lineTo(W, H - 1);
			ctx.stroke();

			if (running) {
				// Physics
				planeVel += 0.15; // gravity
				planeY += planeVel;
				dist += speed;
				speed = 2.5 + dist / 2000;
				frame++;

				// Spawn mountains
				if (mountains.length === 0 || mountains[mountains.length - 1].x < W - 200) {
					spawnMountain();
				}

				// Update mountains
				for (const m of mountains) {
					m.x -= speed;
					if (!m.passed && m.x < 60) {
						m.passed = true;
						score++;
					}
				}
				mountains = mountains.filter(m => m.x > -40);

				// Trail particles
				if (frame % 2 === 0) addTrail();

				// Update particles
				for (const p of particles) {
					p.x += p.vx;
					p.y += p.vy;
					p.life -= 0.03;
				}
				particles = particles.filter(p => p.life > 0);

				// Collision
				const planeL = 48;
				const planeH = 28;
				const px = 70;

				if (planeY < 5 || planeY > H - 5) {
					running = false;
					gameOver = true;
					if (score > highScore) highScore = score;
				}

				for (const m of mountains) {
					if (px + 15 > m.x - 30 && px - 15 < m.x + 30) {
						if (planeY - 10 < m.gapY || planeY + 10 > m.gapY + m.gapH) {
							running = false;
							gameOver = true;
							if (score > highScore) highScore = score;
						}
					}
				}
			}

			// Draw particles
			for (const p of particles) {
				ctx.globalAlpha = p.life;
				ctx.fillStyle = p.color;
				ctx.fillRect(p.x, p.y, 2, 2);
			}
			ctx.globalAlpha = 1;

			// Draw mountains
			for (const m of mountains) drawMountain(m);

			// Draw plane
			drawPlane(70, planeY);

			// HUD
			ctx.fillStyle = '#39ff14';
			ctx.font = '14px "Space Mono", monospace';
			ctx.textAlign = 'left';
			ctx.fillText(`SCORE: ${score}`, 15, 25);
			ctx.fillStyle = '#00f0ff';
			ctx.fillText(`DIST: ${Math.floor(dist)}m`, 15, 45);
			ctx.fillStyle = '#b026ff';
			ctx.textAlign = 'right';
			ctx.fillText(`SPEED: ${speed.toFixed(1)}x`, W - 15, 25);

			requestAnimationFrame(loop);
		}

		function flap() {
			if (!running && !gameOver) {
				reset();
			} else if (gameOver) {
				reset();
			} else {
				planeVel = -4.5;
			}
		}

		canvas.addEventListener('click', flap);
		canvas.addEventListener('touchstart', (e) => { e.preventDefault(); flap(); });

		function handleKey(e: KeyboardEvent) {
			if (e.code === 'Space' || e.code === 'ArrowUp') {
				e.preventDefault();
				flap();
			}
		}
		window.addEventListener('keydown', handleKey);

		// Draw initial state
		loop();

		return () => {
			window.removeEventListener('keydown', handleKey);
		};
	});
</script>

<div class="min-h-screen px-6 py-20 max-w-3xl mx-auto">
	<a href="/games" class="font-mono text-xs text-gray-600 hover:text-neon-green transition-colors">← back to games</a>

	<h1 class="font-[family-name:var(--font-family-display)] text-4xl md:text-5xl font-bold mt-8 mb-2">
		<span class="text-neon-blue" style="text-shadow: 0 0 10px #00f0ff, 0 0 40px #00f0ff;">night flight</span>
	</h1>
	<p class="font-mono text-sm text-gray-500 mb-8">dodge the mountains. how far can you fly?</p>

	<div class="relative">
		<canvas
			bind:this={canvas}
			class="w-full max-w-[600px] rounded-lg border border-dark-border cursor-pointer"
			style="aspect-ratio: 600/400; image-rendering: pixelated;"
		></canvas>

		{#if !gameStarted}
			<div class="absolute inset-0 flex items-center justify-center max-w-[600px]">
				<div class="text-center">
					<p class="font-mono text-neon-green text-lg mb-2">✈️ NIGHT FLIGHT</p>
					<p class="font-mono text-gray-400 text-sm mb-4">click or press space to fly</p>
					<p class="font-mono text-gray-600 text-xs">avoid the mountains</p>
				</div>
			</div>
		{/if}

		{#if gameOver}
			<div class="absolute inset-0 flex items-center justify-center max-w-[600px] bg-black/60 rounded-lg">
				<div class="text-center">
					<p class="font-mono text-neon-pink text-xl mb-2">CRASHED</p>
					<p class="font-mono text-white text-sm">score: {score}</p>
					{#if highScore > 0}
						<p class="font-mono text-neon-yellow text-xs mt-1">best: {highScore}</p>
					{/if}
					<p class="font-mono text-gray-500 text-xs mt-4">click or press space to retry</p>
				</div>
			</div>
		{/if}
	</div>

	<div class="mt-6 font-mono text-xs text-gray-600 space-y-1">
		<p>controls: <span class="text-gray-400">click / tap / space / ↑</span></p>
		<p>goal: <span class="text-gray-400">fly through gaps, don't hit mountains</span></p>
	</div>
</div>
