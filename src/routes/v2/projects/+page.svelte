<script lang="ts">
	const projects = [
		{
			name: 'jacksonmathis.com',
			desc: 'My personal website — two editions. Hacker mode and Pilot mode.',
			tech: ['SvelteKit', 'TypeScript', 'Tailwind'],
			status: 'OPERATIONAL',
			callsign: 'ALPHA-1',
		},
		{
			name: 'Night Flight',
			desc: 'Canvas-based flight game. Dodge mountains, fly through the night.',
			tech: ['Canvas', 'Physics', 'JavaScript'],
			status: 'OPERATIONAL',
			callsign: 'BRAVO-2',
		},
		{
			name: 'Field Goal Kicker',
			desc: 'Physics football game with wind, angle, and power mechanics.',
			tech: ['Canvas', 'Physics', 'JavaScript'],
			status: 'OPERATIONAL',
			callsign: 'CHARLIE-3',
		},
		{
			name: 'More Incoming',
			desc: 'New missions being planned. Check back for updates.',
			tech: ['???'],
			status: 'PENDING',
			callsign: 'DELTA-4',
		},
	];
</script>

<div class="min-h-screen px-6 py-16 max-w-3xl mx-auto">
	<a href="/v2" class="text-xs transition-colors duration-200" style="color: var(--av-amber-dim);"
		onmouseenter={(e) => e.currentTarget.style.color = 'var(--av-amber)'}
		onmouseleave={(e) => e.currentTarget.style.color = 'var(--av-amber-dim)'}>
		← RETURN TO COCKPIT
	</a>

	<h1 class="text-4xl md:text-5xl font-bold mt-8 mb-2" style="font-family: 'Orbitron', sans-serif;">
		<span class="glow-amber" style="color: var(--av-amber);">FLIGHT LOG</span>
	</h1>
	<p class="text-xs mb-12" style="color: var(--av-amber-dim);">COMPLETED AND ACTIVE MISSIONS</p>

	<div class="space-y-4">
		{#each projects as project}
			<div class="instrument-panel p-5 transition-all duration-200"
				onmouseenter={(e) => e.currentTarget.style.borderColor = 'var(--av-amber)'}
				onmouseleave={(e) => e.currentTarget.style.borderColor = 'var(--av-panel-border)'}>
				<div class="flex items-start justify-between mb-3">
					<div>
						<p class="text-[10px] tracking-widest" style="color: var(--av-amber-dim);">{project.callsign}</p>
						<h2 class="text-lg font-bold mt-1" style="color: var(--av-white);">{project.name}</h2>
					</div>
					<span class="text-[10px] px-2 py-0.5 rounded"
						style="border: 1px solid {project.status === 'OPERATIONAL' ? 'var(--av-green-dim)' : 'var(--av-amber-dim)'};
							   color: {project.status === 'OPERATIONAL' ? 'var(--av-green)' : 'var(--av-amber-dim)'};">
						{project.status}
					</span>
				</div>
				<p class="text-xs mb-3" style="color: var(--av-white); opacity: 0.5;">{project.desc}</p>
				<div class="flex gap-2">
					{#each project.tech as t}
						<span class="text-[10px] px-2 py-0.5 rounded"
							style="border: 1px solid var(--av-panel-border); color: var(--av-amber-dim);">
							{t}
						</span>
					{/each}
				</div>
			</div>
		{/each}
	</div>
</div>
