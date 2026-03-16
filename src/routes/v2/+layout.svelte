<script lang="ts">
	let { children } = $props();
</script>

<svelte:head>
	<title>Jackson Mathis — Pilot Edition</title>
	<meta name="description" content="Jackson Mathis — future pilot, builder, gamer." />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
	<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Share+Tech+Mono&display=swap" rel="stylesheet" />
</svelte:head>

<div class="v2-aviation min-h-screen">
	{@render children()}
</div>

<style>
	:global(.v2-aviation) {
		--av-amber: #ffb000;
		--av-amber-dim: #8b6914;
		--av-green: #00ff88;
		--av-green-dim: #006633;
		--av-red: #ff3333;
		--av-sky: #1a2744;
		--av-panel: #0d1117;
		--av-panel-border: #1c2a3a;
		--av-instrument: #0a0f18;
		--av-white: #c8d6e5;
		background: linear-gradient(180deg, #060a10 0%, #0d1520 50%, #0a1020 100%);
		color: var(--av-white);
		font-family: 'Share Tech Mono', monospace;
		min-height: 100vh;
	}

	:global(.v2-aviation ::selection) {
		background-color: var(--av-amber);
		color: #000;
	}

	:global(.v2-aviation .instrument-panel) {
		background: var(--av-instrument);
		border: 1px solid var(--av-panel-border);
		border-radius: 8px;
		box-shadow:
			inset 0 1px 0 rgba(255, 176, 0, 0.05),
			0 4px 20px rgba(0, 0, 0, 0.4);
	}

	:global(.v2-aviation .glow-amber) {
		text-shadow: 0 0 8px var(--av-amber), 0 0 30px rgba(255, 176, 0, 0.3);
	}

	:global(.v2-aviation .glow-av-green) {
		text-shadow: 0 0 8px var(--av-green), 0 0 30px rgba(0, 255, 136, 0.3);
	}

	/* Radar sweep animation */
	@keyframes -global-radar-sweep {
		0% { transform: rotate(0deg); }
		100% { transform: rotate(360deg); }
	}

	@keyframes -global-blink-slow {
		0%, 100% { opacity: 1; }
		50% { opacity: 0.3; }
	}

	@keyframes -global-altitude-rise {
		0% { transform: translateY(20px); opacity: 0; }
		100% { transform: translateY(0); opacity: 1; }
	}

	@keyframes -global-horizon-drift {
		0%, 100% { transform: rotate(-2deg); }
		50% { transform: rotate(2deg); }
	}
</style>
