<script lang="ts">
	import { onMount } from 'svelte';

	let canvas: HTMLCanvasElement;
	let score = $state(0);
	let attempts = $state(0);
	let phase = $state<'aiming' | 'powering' | 'flying' | 'result'>('aiming');
	let angle = $state(45);
	let power = $state(0);
	let powerDir = $state(1);
	let wind = $state(0);
	let resultText = $state('');
	let resultColor = $state('');

	// Ball state
	let ballX = 0;
	let ballY = 0;
	let ballVx = 0;
	let ballVy = 0;
	let ballSize = 8;
	let trail: { x: number; y: number; alpha: number }[] = [];

	// Field dimensions (relative to canvas)
	const GROUND_Y = 0.85;
	const KICK_X = 0.25;
	const POST_X = 0.75;
	const POST_WIDTH = 0.12;
	const POST_HEIGHT = 0.25;
	const CROSSBAR_Y = 0.55;

	let animFrame: number;

	function newWind() {
		wind = Math.round((Math.random() - 0.5) * 20);
	}

	function resetBall() {
		phase = 'aiming';
		power = 0;
		powerDir = 1;
		angle = 45;
		resultText = '';
		trail = [];
		newWind();
	}

	function startPower() {
		if (phase !== 'aiming') return;
		phase = 'powering';
	}

	function kick() {
		if (phase !== 'powering') return;
		phase = 'flying';
		attempts++;

		const w = canvas.width;
		const h = canvas.height;

		ballX = KICK_X * w;
		ballY = GROUND_Y * h;

		const rad = (angle * Math.PI) / 180;
		const speed = power * 0.18;
		ballVx = Math.cos(rad) * speed;
		ballVy = -Math.sin(rad) * speed;
	}

	onMount(() => {
		const ctx = canvas.getContext('2d')!;

		const resize = () => {
			canvas.width = canvas.clientWidth * 2;
			canvas.height = canvas.clientHeight * 2;
		};
		resize();
		window.addEventListener('resize', resize);
		newWind();

		function draw() {
			const w = canvas.width;
			const h = canvas.height;
			ctx.clearRect(0, 0, w, h);

			// Sky gradient
			const sky = ctx.createLinearGradient(0, 0, 0, h * GROUND_Y);
			sky.addColorStop(0, '#0a0a1a');
			sky.addColorStop(1, '#1a1a2e');
			ctx.fillStyle = sky;
			ctx.fillRect(0, 0, w, h * GROUND_Y);

			// Stars
			ctx.fillStyle = 'rgba(255,255,255,0.3)';
			for (let i = 0; i < 30; i++) {
				const sx = ((i * 137.5) % w);
				const sy = ((i * 73.3) % (h * 0.4));
				ctx.fillRect(sx, sy, 1.5, 1.5);
			}

			// Ground
			ctx.fillStyle = '#1a3d1a';
			ctx.fillRect(0, h * GROUND_Y, w, h * (1 - GROUND_Y));

			// Yard lines
			ctx.strokeStyle = 'rgba(255,255,255,0.1)';
			ctx.lineWidth = 1;
			for (let i = 0; i < 10; i++) {
				const lx = w * (0.1 + i * 0.08);
				ctx.beginPath();
				ctx.moveTo(lx, h * GROUND_Y);
				ctx.lineTo(lx, h);
				ctx.stroke();
			}

			// Goal post
			const postLeft = (POST_X - POST_WIDTH / 2) * w;
			const postRight = (POST_X + POST_WIDTH / 2) * w;
			const postTop = CROSSBAR_Y * h;
			const postBottom = GROUND_Y * h;
			const postMid = POST_X * w;

			ctx.strokeStyle = '#ffff00';
			ctx.lineWidth = 4;
			ctx.shadowColor = '#ffff00';
			ctx.shadowBlur = 10;

			// Left upright
			ctx.beginPath();
			ctx.moveTo(postLeft, postTop - h * 0.12);
			ctx.lineTo(postLeft, postTop);
			ctx.stroke();

			// Right upright
			ctx.beginPath();
			ctx.moveTo(postRight, postTop - h * 0.12);
			ctx.lineTo(postRight, postTop);
			ctx.stroke();

			// Crossbar
			ctx.beginPath();
			ctx.moveTo(postLeft, postTop);
			ctx.lineTo(postRight, postTop);
			ctx.stroke();

			// Center post
			ctx.strokeStyle = '#888';
			ctx.shadowBlur = 0;
			ctx.lineWidth = 3;
			ctx.beginPath();
			ctx.moveTo(postMid, postTop);
			ctx.lineTo(postMid, postBottom);
			ctx.stroke();

			// Wind indicator
			ctx.fillStyle = '#666';
			ctx.font = `${h * 0.03}px monospace`;
			ctx.textAlign = 'center';
			const windLabel = wind > 0 ? `wind: ${wind} →` : wind < 0 ? `wind: ← ${Math.abs(wind)}` : 'wind: none';
			ctx.fillText(windLabel, w / 2, h * 0.06);

			// Aiming arc
			if (phase === 'aiming' || phase === 'powering') {
				const kickX = KICK_X * w;
				const kickY = GROUND_Y * h;

				// Draw football on ground
				ctx.fillStyle = '#8B4513';
				ctx.beginPath();
				ctx.ellipse(kickX, kickY - 10, 12, 8, -0.3, 0, Math.PI * 2);
				ctx.fill();
				ctx.strokeStyle = '#fff';
				ctx.lineWidth = 1;
				ctx.beginPath();
				ctx.moveTo(kickX - 4, kickY - 14);
				ctx.lineTo(kickX + 4, kickY - 6);
				ctx.stroke();

				// Aim line
				const rad = (angle * Math.PI) / 180;
				const lineLen = 60 + power * 0.8;
				ctx.strokeStyle = `rgba(57, 255, 20, ${0.4 + power / 200})`;
				ctx.lineWidth = 2;
				ctx.setLineDash([6, 4]);
				ctx.beginPath();
				ctx.moveTo(kickX, kickY);
				ctx.lineTo(kickX + Math.cos(rad) * lineLen, kickY - Math.sin(rad) * lineLen);
				ctx.stroke();
				ctx.setLineDash([]);
			}

			// Ball trail
			for (const t of trail) {
				ctx.fillStyle = `rgba(57, 255, 20, ${t.alpha})`;
				ctx.beginPath();
				ctx.arc(t.x, t.y, 3, 0, Math.PI * 2);
				ctx.fill();
				t.alpha -= 0.015;
			}
			trail = trail.filter(t => t.alpha > 0);

			// Flying ball
			if (phase === 'flying') {
				ballVy += 0.35; // gravity
				ballVx += wind * 0.003; // wind
				ballX += ballVx;
				ballY += ballVy;

				trail.push({ x: ballX, y: ballY, alpha: 0.6 });

				// Draw football
				const velAngle = Math.atan2(ballVy, ballVx);
				ctx.save();
				ctx.translate(ballX, ballY);
				ctx.rotate(velAngle);
				ctx.fillStyle = '#8B4513';
				ctx.beginPath();
				ctx.ellipse(0, 0, 12, 7, 0, 0, Math.PI * 2);
				ctx.fill();
				ctx.strokeStyle = '#fff';
				ctx.lineWidth = 1.5;
				ctx.beginPath();
				ctx.moveTo(-3, -5);
				ctx.lineTo(3, 5);
				ctx.stroke();
				ctx.restore();

				ctx.shadowBlur = 0;

				// Check if ball crosses goal post plane
				if (ballX >= postLeft && ballX <= postRight && ballY <= postTop && ballY >= postTop - h * 0.12 && ballVx > 0) {
					// GOOD!
					score++;
					phase = 'result';
					resultText = 'GOOD! 🏈';
					resultColor = '#39ff14';
				} else if (ballY > GROUND_Y * h || ballX > w || ballX < 0 || ballY < -50) {
					// Miss
					phase = 'result';
					if (ballX >= postLeft && ballX <= postRight) {
						resultText = 'TOO LOW!';
					} else if (ballX > postRight) {
						resultText = 'WIDE RIGHT!';
					} else if (ballX < postLeft && ballX > KICK_X * w + 50) {
						resultText = 'WIDE LEFT!';
					} else {
						resultText = 'NO GOOD!';
					}
					resultColor = '#ff2d95';
				}
			}

			// Result text
			if (phase === 'result') {
				ctx.font = `bold ${h * 0.08}px monospace`;
				ctx.textAlign = 'center';
				ctx.fillStyle = resultColor;
				ctx.shadowColor = resultColor;
				ctx.shadowBlur = 20;
				ctx.fillText(resultText, w / 2, h * 0.4);
				ctx.shadowBlur = 0;

				ctx.font = `${h * 0.03}px monospace`;
				ctx.fillStyle = '#666';
				ctx.fillText('click to kick again', w / 2, h * 0.48);
			}

			animFrame = requestAnimationFrame(draw);
		}
		draw();

		return () => {
			cancelAnimationFrame(animFrame);
			window.removeEventListener('resize', resize);
		};
	});

	// Power meter animation
	$effect(() => {
		if (phase !== 'powering') return;
		const interval = setInterval(() => {
			power += powerDir * 3;
			if (power >= 100) { power = 100; powerDir = -1; }
			if (power <= 0) { power = 0; powerDir = 1; }
		}, 20);
		return () => clearInterval(interval);
	});
</script>

<div class="min-h-screen px-6 py-20 max-w-3xl mx-auto">
	<a href="/" class="font-mono text-xs text-gray-600 hover:text-neon-green transition-colors">← back</a>

	<h1 class="font-[family-name:var(--font-family-display)] text-4xl md:text-5xl font-bold mt-8 mb-2">
		<span class="text-neon-yellow" style="text-shadow: 0 0 10px #fff01f, 0 0 40px #fff01f;">/games</span>
	</h1>
	<p class="text-gray-500 font-mono text-sm mb-8">field goal kicker</p>

	<!-- Score -->
	<div class="flex items-center justify-between mb-4 font-mono text-sm">
		<span class="text-gray-400">score: <span class="text-neon-green">{score}</span> / {attempts}</span>
		<span class="text-gray-600">{attempts > 0 ? Math.round((score / attempts) * 100) : 0}% accuracy</span>
	</div>

	<!-- Game canvas -->
	<div class="relative">
		<canvas
			bind:this={canvas}
			class="w-full h-80 md:h-96 rounded-lg border border-dark-border cursor-crosshair bg-dark"
			onmousedown={startPower}
			onmouseup={kick}
			ontouchstart={startPower}
			ontouchend={kick}
			onclick={() => { if (phase === 'result') resetBall(); }}
		></canvas>

		<!-- Power meter -->
		{#if phase === 'aiming' || phase === 'powering'}
			<div class="absolute left-3 bottom-3 w-6 h-32 bg-dark border border-dark-border rounded-full overflow-hidden">
				<div class="absolute bottom-0 w-full rounded-full transition-all duration-75"
					style="height: {power}%; background: linear-gradient(to top, #39ff14, #fff01f, #ff2d95);">
				</div>
			</div>

			<!-- Angle control -->
			<div class="absolute right-3 bottom-3 flex flex-col gap-1">
				<button onclick={() => angle = Math.min(80, angle + 5)}
					class="font-mono text-xs text-gray-500 hover:text-neon-green bg-dark border border-dark-border rounded px-2 py-1 cursor-pointer">▲</button>
				<span class="font-mono text-xs text-gray-400 text-center">{angle}°</span>
				<button onclick={() => angle = Math.max(15, angle - 5)}
					class="font-mono text-xs text-gray-500 hover:text-neon-green bg-dark border border-dark-border rounded px-2 py-1 cursor-pointer">▼</button>
			</div>
		{/if}
	</div>

	<!-- Instructions -->
	<div class="mt-6 bg-dark-card border border-dark-border rounded-lg p-4 font-mono text-xs text-gray-500 space-y-1">
		<p>▲▼ adjust angle · <span class="text-gray-400">hold</span> to charge power · <span class="text-gray-400">release</span> to kick</p>
		<p>watch the wind — it pushes the ball</p>
	</div>

	<!-- Nav to other pages -->
	<div class="mt-8 flex gap-4">
		<a href="/lab" class="font-mono text-xs text-gray-600 hover:text-neon-pink transition-colors">/lab</a>
		<a href="/projects" class="font-mono text-xs text-gray-600 hover:text-neon-blue transition-colors">/projects</a>
	</div>
</div>
